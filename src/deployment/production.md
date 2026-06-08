# Production Deployment

Running Botmarley for real means three things on top of the basic install: a proper database, a way to reach it over HTTPS, and keeping it running across reboots. Optionally, you route exchange calls through a fixed IP.

## Database

Don't run the development database in production. Point Botmarley at a real PostgreSQL instance with the `DATABASE_URL` environment variable:

```bash
DATABASE_URL="postgresql://user:password@host:5432/botmarley"
```

Set it in your service definition (below) so it survives restarts.

## Keep it running with systemd

A minimal unit file:

```ini
[Unit]
Description=Botmarley
After=network.target

[Service]
ExecStart=/home/youruser/botmarley/server
WorkingDirectory=/home/youruser/botmarley
Environment=DATABASE_URL=postgresql://user:password@host:5432/botmarley
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable --now botmarley
```

`Restart=always` brings it back if it crashes, and `enable` brings it back after a reboot.

## Expose it over HTTPS with Cloudflare Tunnel

You don't want the trading UI on a bare port open to the internet. A Cloudflare Tunnel gives you HTTPS on a real hostname without opening any inbound ports. The bot listens on localhost; `cloudflared` connects out to Cloudflare and forwards traffic in.

The reference setup runs two services behind one tunnel:

| Public hostname | Forwards to |
|-----------------|-------------|
| `botmarley.lipinski.work` | `http://localhost:5000` (the bot) |
| `keymarley.lipinski.work` | `http://localhost:8000` (the licence server) |

In Cloudflare's dashboard, add a public hostname for each, with the service pointing at the local port. That's the whole ingress. Because the bot only listens on localhost, nothing is reachable except through the tunnel.

If you later move a service to a different host, you change the tunnel target in Cloudflare. Botmarley reads its endpoints from configuration, so nothing needs recompiling.

## Route exchange calls through a fixed IP

Some exchange accounts are locked to a fixed IP for API access. Botmarley can send its exchange requests through an SSH SOCKS5 tunnel so they always leave from the same address.

Configure the SSH proxy in settings (host, port, user). Botmarley opens the tunnel, watches it, and reopens it if it drops. Once it's set, exchange calls route through that host. The reference deployment tunnels through a small always-on box and points exchange traffic at it.

## Updating

Replace the binary and restart the service:

```bash
sudo systemctl restart botmarley
```

Your data in `~/.botmarley` and your database are untouched by an update.
