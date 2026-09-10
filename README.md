# Local Neon stack

A tiny Docker Compose setup that mimics [Neon](https://neon.tech) serverless
Postgres locally: a plain Postgres 17 server plus the
[Neon serverless HTTP/WebSocket proxy](https://github.com/timowilhelm/local-neon-http-proxy)
in front of it. Apps using the `@neondatabase/serverless` driver (or a
framework helper like the Next.js `neon()` driver) connect to the proxy with no
code changes, while pgAdmin gives you a GUI over the same database.

No cloud account, no Neon API keys, no secrets. Everything runs on localhost.

## What you get

| Service | Port | Purpose |
| --- | --- | --- |
| `postgres` | `5432` | Real Postgres 17 (matches Neon's server version) |
| `neon-proxy` | `4444` | Speaks Neon's serverless HTTP/WS protocol in front of Postgres |
| `pgadmin` | `5050` | Web UI for poking at the database |

## Quick start

```sh
cp .env.example .env
docker compose up -d
```

Then point your app at the proxy. For the Next.js Neon driver:

```
DATABASE_URL=postgres://postgres:postgres@localhost:5432/main
DATABASE_URL_WS=ws://localhost:4444
```

The `DATABASE_URL_WS` style setup is what the
[`neon()` serverless driver](https://neon.com/docs/local/local-neon-http-proxy)
expects: the first URL is a plain Postgres connection used for config/queries,
and the proxy handles the WebSocket-based serverless traffic.

## Migrations and seeding

Run your normal tooling against `postgres://postgres:postgres@localhost:5432/main`.
For example, with Drizzle:

```sh
DATABASE_URL=postgres://postgres:postgres@localhost:5432/main pnpm drizzle-kit push
```

## pgAdmin

Open http://localhost:5050 and log in with the credentials from `.env`
(`admin@local.dev` / `admin` by default). Register a server once:

- Host: `postgres`
- Port: `5432`
- Username / password: from `.env` (default `postgres` / `postgres`)

pgAdmin shares the Compose network with Postgres, so `postgres` as the hostname
works without publishing anything extra.

## Configuration

All knobs live in `.env` (see `.env.example` for the full list). Everything has
a safe local default, so the stack works with an empty `.env` too:

- `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`, `POSTGRES_PORT`
- `PROXY_PORT`
- `PGADMIN_DEFAULT_EMAIL`, `PGADMIN_DEFAULT_PASSWORD`, `PGADMIN_PORT`

## Data lifecycle

Named volumes `postgres_data` and `pgadmin_data` survive `docker compose down`.
Wipe them with:

```sh
docker compose down -v
```

## Requirements

- Docker Engine 24+ or Docker Desktop (Compose v2 built in)
- Roughly 300 MB of disk for the three images

## Credits

Built around [timowilhelm/local-neon-http-proxy](https://github.com/timowilhelm/local-neon-http-proxy)
and upstream [neondatabase](https://github.com/neondatabase) images.
