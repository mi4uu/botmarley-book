# Installation

Botmarley ships as a single server binary plus a PostgreSQL database. You need both running.

## Requirements

- A Linux, macOS, or Windows machine you control (a small VPS is plenty).
- Docker, for the database. Or your own PostgreSQL if you prefer.
- About 200 MB of disk for the binary and growing space for market data.

## 1. Get the binary

Download the latest release from [GitHub Releases](https://github.com/mi4uu/botmarley/releases/latest) and put it somewhere on your PATH, or just keep it in a working folder.

## 2. Start PostgreSQL

The quickest way is Docker:

```bash
docker compose up -d postgres
```

That starts a database with the default development credentials. For a real deployment, set your own connection string with the `DATABASE_URL` environment variable. See [Production Deployment](../deployment/production.md).

## 3. Run the server

```bash
./server
```

On first start it creates the tables it needs and writes its working files to `~/.botmarley`.

## 4. Open the app

Go to [http://localhost:3000](http://localhost:3000). You should see the dashboard.

That's the whole install. Next, walk through the [Quick Start](quick-start.md) to run your first backtest, or open the [live demo](../demo.md) to look around first.

## Where Botmarley keeps its files

- `~/.botmarley/data` holds historical candle data in Apache Arrow format.
- `~/.botmarley/.license_cache` holds your validated licence (signed, with a grace period).
- Strategies live in `strats/` next to the binary, split into `builtin`, `example`, and `user`.

## Environment variables

| Variable | What it does |
|----------|--------------|
| `DATABASE_URL` | PostgreSQL connection string. Overrides the default. |
| `KEYMARLEY_VALIDATE_URL` | Licence server endpoint. Defaults to the hosted one. See [Licensing](../deployment/licensing.md). |
| `BACKTEST_SLIPPAGE_BPS` | Optional slippage in basis points applied during backtests. Off by default. |
