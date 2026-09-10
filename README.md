# local-neon 🏄

**So Neon's price hikes caught you off guard, and now you can no longer afford to use Neon branches locally?**

Yeah. Same. 😤

Here's the solution: a real Postgres 17 plus the Neon serverless HTTP/WebSocket proxy, wrapped in one Docker Compose file. Your `neon()` driver keeps working. Your branches keep working. Your wallet keeps working. ☕

No cloud account. No API keys. No compute bills that look like a phone number. Just `docker compose up` and you're back in business.

## The short version

```sh
git clone https://github.com/thecaffeinatedengineer/local-neon
cd local-neon
cp .env.example .env
docker compose up -d
```

Point your app at it:

```sh
# plain Postgres — migrations, seeds, drizzle-kit, whatever
DATABASE_URL=postgres://postgres:postgres@localhost:5432/main

# Neon serverless driver — talks to the proxy
DATABASE_URL_WS=ws://localhost:4444
```

That's it. Your code doesn't change. Your `.env` barely changes. The cloud doesn't change... because there is no cloud. 🌤️

## What's inside

| Service | Port | What it does |
| --- | --- | --- |
| `postgres` | `5432` | Real Postgres 17, same version Neon runs |
| `neon-proxy` | `4444` | Speaks the Neon serverless HTTP/WS protocol |
| `pgadmin` | `5050` | GUI for poking at your data |

## Wait, what about branches?

Neon's killer feature is copy-on-write branches. Plain Postgres doesn't have them... but [CoW filesystems do](https://zfsproject.org/), and so does `pg_dump`. This stack keeps the driver story 100% Neon-compatible so your local dev matches production, and pairs it with whatever branching workflow you like.

Want proper database branching? Snapshot with `pg_dump`, restore into a fresh database, point your proxy at it. Or run a second Postgres container and diff them. It's your machine, your rules, $0/month.

## The bill

A Neon Scale plan starts around $69/month. This stack runs on a laptop you already own.

**Savings: $828/year.** Enough for a lot of coffee. ☕☕☕

## Requirements

- Docker Engine 24+ / Docker Desktop
- ~300 MB disk for images
- The smug feeling of self-hosting

## Credits

Built on [timowilhelm/local-neon-http-proxy](https://github.com/timowilhelm/local-neon-http-proxy) and the upstream [Neon](https://github.com/neondatabase) images. MIT licensed.
