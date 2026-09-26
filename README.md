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
4. Added a monitor for a known-bad target to confirm "down" status and alerting actually trigger
5. Documented both states with screenshots

## How to Run It

1. Open Portainer → Stacks → Add stack
2. Name it uptime-kuma, paste in docker-compose.yml from this repo, and deploy
3. Open http://localhost:3001 and complete the admin setup

## Screenshots

| Step | Screenshot |
|------|------------|
| Portainer stack deployed | screenshots/01-stack-deployed.png |
| Monitor up (healthy) | screenshots/02-monitor-up.png |
| Monitor down (alert triggered) | screenshots/03-monitor-down.png |

## Credit

Project idea from Joe (@joecoxtech) — "Build Your Way Into IT: Five Free Home Lab Projects."
