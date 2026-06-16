# Varaždinski streličarski klub

A full-stack website built for an archery club in Croatia — a public site (news,
schedule, roster with interactive 3D bow viewers, achievements, sponsors, club
history, contact forms) in Croatian and English, plus an admin area for managing
content.

**Live:** <https://archery.axlothecook.com> — self-hosted on a Raspberry Pi, exposed
via Cloudflare Tunnel.

The project is split across several repositories. Pick the one you want to view:

| Repository | What it is |
| --- | --- |
| [**Front end**](https://github.com/axlothecook/Archery-club-front-end) | SvelteKit 2 / Svelte 5 app. The public site UI — animated pages, 3D bow viewers (Threlte / three.js), HR/EN locale switching. |
| [**Back end**](https://github.com/axlothecook/Archery-club-backend) | Node.js + Express + Prisma 7 + PostgreSQL REST API. Content, achievements, inquiries (email), session-auth admin, Google Cloud Translation backfill. |
| [**Shared contracts**](https://github.com/axlothecook/Archery-contracts) | Shared TypeScript types + Zod schemas used by both the front end and back end. |

> The deploy repo (Docker Compose + Cloudflare Tunnel orchestration for the Pi) is
> kept private.

## How it fits together

```
Browser ─▶ Cloudflare Tunnel ─▶ nginx (same-origin proxy)
                                  ├─▶ /      Front end (SvelteKit, adapter-node)
                                  └─▶ /api   Back end (Express) ─▶ PostgreSQL
                                                                └─▶ Cloudflare R2 (images)
                                                                └─▶ Google Cloud Translation
```

- **Same-origin** topology: the front end and the `/api` path are served from one
  origin behind nginx, so the admin session cookie is first-party (`SameSite=Lax`)
  and never dropped by strict browsers.
- The **front end** is server-rendered (adapter-node); locale is cookie-driven
  (HR / EN) and resolved per-request in the layout load.
- The **back end** owns all data via Prisma 7 + PostgreSQL; images live in
  Cloudflare R2; content is translated into English with Google Cloud Translation
  and stored alongside the originals.
- The **deploy** stack ties everything together with Docker Compose (`db`, `backend`,
  `frontend`, `proxy`, `cloudflared`) on a Raspberry Pi. CI builds the arm64 images
  and auto-deploys on every push to `main`.

## Tech stack at a glance

- **Front end:** SvelteKit 2 / Svelte 5 (runes), Threlte / three.js (3D bow viewer),
  GSAP + View Transitions API (page animations), Sass (own published library),
  cookie-driven i18n.
- **Back end:** Node.js, Express, Prisma 7 / PostgreSQL, Zod, session-cookie auth,
  Google Cloud Translation, Brevo (transactional email).
- **Infra:** Docker / Docker Compose, nginx (same-origin reverse proxy), GitHub
  Actions (GHCR, arm64), Tailscale (CI → Pi), Cloudflare Tunnel + R2, nightly
  `pg_dump` backup, Raspberry Pi.

## Features

- Public site: homepage with auto-crossfading hero + animated sections, weekly /
  monthly training schedule with a swipeable calendar, full roster with per-archer
  profile pages (interactive 3D bow viewer, real achievements, coaches & students),
  achievements, sponsors, club history, and contact / membership / sponsor / donation
  inquiry forms that send email.
- Fully responsive, with a dedicated mobile pass across every page.
- Croatian + English, switchable via a cookie-driven language toggle.
- Self-hosted on a Raspberry Pi with a containerised CI/CD pipeline and a nightly,
  restore-verified database backup.
