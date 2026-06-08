# Licensing & KeyMarley

Botmarley checks a licence before it unlocks. The check is done by a small companion service called **KeyMarley**, and it's built so that a network blip never locks you out of your own bot.

## How the check works

1. You enter your licence key on the settings page.
2. Botmarley sends it to the KeyMarley server, which returns a signed response.
3. Botmarley verifies the signature with a built-in public key and caches the result on disk.
4. It rechecks on boot and every 24 hours.

The signature uses Ed25519. The private signing key never lives on any deployed machine, so a signed response can't be forged even if someone has the binary.

## The grace period

If the licence server is briefly unreachable, Botmarley falls back to the cached response and keeps running for a grace window of 48 hours. So a short outage on the licence server, or a momentary network drop, doesn't stop your bot. After the grace window with no successful check, the licence is treated as invalid.

The cache stores only the signed date, the signature, and a hash of your key. Your raw key is never written to disk.

## Pointing at your own KeyMarley

By default Botmarley talks to the hosted licence server at `https://keymarley.lipinski.work`. If you run your own, override the endpoint with an environment variable:

```bash
KEYMARLEY_VALIDATE_URL="https://your-host.example.com/api/validate"
```

Because the URL is read from configuration, you can move the licence server to another host without rebuilding Botmarley.

## Running KeyMarley yourself

KeyMarley is a small Axum service backed by SQLite. It listens on a local port (8000 in the reference setup) and is exposed over HTTPS through the same Cloudflare Tunnel as the bot. Keep it always-on with systemd the same way as the main service, so the licence check is there whenever a bot asks.
