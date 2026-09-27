# Uptime Kuma Homelab Monitoring

A self-hosted uptime and alerting dashboard, deployed as a Docker stack through Portainer, running on WSL2 (Ubuntu 22.04).

## Why This Project

Uptime monitoring is core to what a network operations center does all day — watching services, catching outages, and alerting before a customer notices. This project demonstrates deploying and configuring a monitoring stack, then proving it actually detects both healthy and failed services.

## Stack

- **Docker Desktop** (WSL2 backend)
- **WSL2** — Ubuntu 22.04
- **Portainer CE** — used to deploy this as a Stack (builds on the docker-portainer-homelab project)
- **Uptime Kuma** — monitoring dashboard and alerting

## What I Built

1. Deployed Uptime Kuma as a Portainer Stack (not a one-off container) using the Docker Compose definition below
2. Created the admin account and logged into the Kuma dashboard
3. Added a monitor for a known-healthy site to confirm "up" status detection
4. Added a monitor pointed at a URL that returns HTTP 500 on every request (https://httpstat.us/500) to confirm "down" status detection and the alerting path, not just the "up" state
5. Documented both states with screenshots

## How to Run It

1. Open Portainer → Stacks → Add stack
2. Name it uptime-kuma, paste in docker-compose.yml from this repo, and deploy
3. Open http://localhost:3001 and complete the admin setup

## Try It Yourself

Uptime Kuma is running but you do not have to take that on faith. Two things to verify:

**Confirm the down-detection works.** Add a monitor pointed at a URL that reliably returns an error status — `https://httpstat.us/500` returns a 500 on every request and needs no setup. It should show red within a polling cycle, with a heartbeat gap in the UI. A monitoring stack that has never seen a down service is not verified monitoring; it is decoration.

**Confirm alerting path.** Kuma's "down" state is visible in the dashboard, but the operationally important part is the notification path. Kuma supports dozens of channels — email/SMTP, Discord, Slack, ntfy, Telegram, or a generic webhook — configured per-monitor under Settings → Notifications. This repo does not wire up a channel; detection is confirmed, notification is the next step. A monitor without a notification channel is a dashboard, not an alerting system.

## Notes on Production Use

Three things worth knowing about this setup before trusting it with something that matters:

**Polling interval has a real cost.** A 30-second interval on 20 monitors is 57,600 HTTP requests per day. That is fine for a homelab and fine for most teams, but it is worth knowing when sizing: shorter intervals multiply request volume and target-side load linearly.

**A monitor on the same host only sees what the host can see.** If Kuma runs on the same machine as the services it monitors, it detects process and container failures but cannot detect network partitions, DNS failures, or a host that is down entirely — because the monitor goes down with it. Production setups put the probe somewhere else, or run a second instance in a different failure domain.

**State persistence.** Like Vaultwarden and NPM, Kuma keeps its state in a SQLite database inside a named volume (`uptime_kuma_data`). The container can be removed and recreated without losing monitors or history, as long as the volume survives. That also means backups follow the same pattern as any other SQLite-in-a-volume service.

## Screenshots

| Step | Screenshot |
|------|------------|
| Portainer stack deployed | screenshots/01-stack-deployed.png |
| Monitor up (healthy) | screenshots/02-monitor-up.png |
| Monitor down (alert triggered) | screenshots/03-monitor-down.png |

## Related Projects

This repo is one piece of a self-hosted homelab portfolio:

- **[docker-portainer-homelab](https://github.com/shepdogg6t7-glitch/docker-portainer-homelab)** — container management UI (the layer this stack deploys through)
- **[vaultwarden-homelab](https://github.com/shepdogg6t7-glitch/vaultwarden-homelab)** — a service this monitor watches
- **[nginx-proxy-manager-homelab](https://github.com/shepdogg6t7-glitch/nginx-proxy-manager-homelab)** — reverse proxy and TLS termination (fronts the services this monitor watches)

## Credit

Project idea from Joe (@joecoxtech) — "Build Your Way Into IT: Five Free Home Lab Projects."
