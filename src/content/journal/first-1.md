---
title: "Why I Self-Host: Building a Modern Infrastructure with Traefik and Docker"
date: 2026-05-23
excerpt: A look into my VPS setup using Docker, Traefik, and Cloudflare to manage multiple full-stack services.
---

I recently moved my projects to a self-hosted infrastructure. Instead of relying on managed "black-box" platforms, I wanted to build a system where I have full visibility into how my code reaches the internet. 

Here is why I chose this path, the benefits I've seen, and the technical stack powering it all.

## Why I Chose to Self-Host

The primary driver was **control**. In a managed environment, you are limited by the platform's abstractions. By self-hosting on a VPS, I can manage multiple services in separate folders, each with their own lifecycle, while maintaining a unified entry point.

It also serves as a massive learning tool. Configuring your own reverse proxy, managing SSL handshakes, and orchestrating internal Docker networks provides a level of "under-the-hood" knowledge that managed platforms hide from you.

## The Infrastructure Stack

My setup is built to be modular and reproducible. I use a combination of industry-standard tools to keep the "DevOps overhead" low while maintaining high reliability:

*   **VPS (Ubuntu):** The foundational "metal" where everything lives.
*   **Docker & Docker Compose:** Every service (Frontend, API, Database) is containerized. This allows me to run different Node versions or database types on the same machine without conflicts.
*   **Traefik v3:** Acting as my Edge Router. It handles:
    *   **Service Discovery:** Automatically detects new containers via Docker labels.
    *   **SSL Termination:** Automatically fetches and renews certificates from Let's Encrypt.
    *   **Subdomain Routing:** Directing traffic to the correct container based on the Host header.
*   **Cloudflare:** Manages my DNS and provides an extra layer of security and caching via their proxy.
*   **PostgreSQL:** Dedicated database containers isolated within internal Docker networks.

## The Benefits

### 1. Cost Efficiency
Instead of paying per-service fees on platforms like Vercel or Heroku, I pay one flat rate for my VPS. I can host as many staging environments, APIs, and databases as the hardware can handle.

### 2. Professional Routing with Subdomains
With Traefik and Cloudflare, I can easily split my application into specialized subdomains:
*   `teamflo.lalacodes.tech` (Frontend)
*   `api.teamflo.lalacodes.tech` (Backend API)

### 3. Automated SSL
Using the **ACME DNS Challenge** via Cloudflare's API, Traefik handles all my SSL certificates automatically. I never have to manually renew a cert or handle `.pem` files.

### 4. Simplified Deployment
To deploy a new service, I simply add a few labels to a `docker-compose.yaml` file:
```yaml
labels:
  - "traefik.http.routers.my-app.rule=Host(`api.example.com`)"
  - "traefik.http.routers.my-app.tls.certresolver=myresolver"

```
Traefik sees the new labels, configures the route, and requests the SSL certificate instantly.
## Final thoughts

Self-hosting isn't just about saving money; it's about ownership. By building this architecture myself, I’ve created a flexible environment that grows with my projects. Whether it's a small internal tool or a production-grade API, I have the platform ready to host it.