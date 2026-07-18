# Archery club website

A full-stack website built for an archery club in Croatia. It consists of 5 repos: a public site, the backend, an admin dashboard for managing content, shared contracts and deploy repo (listed below):

### [Front end](https://github.com/axlothecook/Archery-club-front-end)
The public site visible to visitors. It contains animated pages, 3D bow viewers, and HR/EN locale switching among other things.

### [Back end](https://github.com/axlothecook/Archery-club-backend)
A server with a database. It contains various content CRUD (like posts, events, etc), email inquiries, session-authentication admin, new admin invites via Brevo email and Google Cloud Translation API.

### [Dashboard](https://github.com/axlothecook/archery-dashboard)
Content Management Dashboard only available to page admins. Manages publicly available content like posts, team roster, event categories, etc.

### [Shared contracts](https://github.com/axlothecook/Archery-contracts)
Shared TypeScript types used by both the public site and backend.

### [Deploy](https://github.com/axlothecook/archery-club-deploy)
Config files and a bash script that act as instructions on how to deploy Docker images. Self-deployed on my Raspberry Pi via a custom [CI/CD pipeline](https://github.com/axlothecook/homelab-ci-cd).


## How it fits together
The diagram below shows how the five repos connect at runtime. One nginx routes each request to the public site, the dashboard, or the API, and the backend owns the data: Postgres, R2 images, and translations.

// graph goes here


# Tech stack
## Frontend
[SvelteKit 2](https://svelte.dev/docs/kit) / [Svelte 5 (runes)](https://svelte.dev): whole app; runes forced project-wide <br />
[Threlte](https://threlte.xyz) and [three.js](https://threejs.org): the 3D bow viewer on the homepage and archer profile pages <br />
[GSAP](https://gsap.com): strip morph animation of navbar pill <br />
[View Transitions API](https://developer.mozilla.org/en-US/docs/Web/API/View_Transition_API): club history chapter open and close animation <br />
[Sass](https://sass-lang.com) + [axlothecook-sass-library](https://github.com/axlothecook/axlothecook-sass-library): global styling <br />
[i18n](https://www.i18next.com): cookie-driven HR/EN switching of all text on the website <br />
[adapter-node](https://svelte.dev/docs/kit/adapter-node): builds the site into a self-contained Node server that renders pages on the server; it's what runs inside the Docker container on the Pi <br />
[Vite 8](https://vite.dev): used for building and development server <br />
[flag-icons](https://github.com/lipis/flag-icons): the individual country flag icons in the language switcher <br />
[@fontsource/inter](https://fontsource.org/fonts/inter): fonts used in public site and dashboard

## Backend
[Node.js](https://nodejs.org) / [Express](https://expressjs.com): runtime and web framework like routing and router middleware <br />
[Prisma 7](https://www.prisma.io): ORM used to communicate with Postgres db <br />
[PostgreSQL](https://www.postgresql.org): used as the db that stores text data and links to R2 stored images and videos <br />
[Zod](https://zod.dev): request validation in body, params and query <br />
[session-cookie auth](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies): `__Host-session` cookie, server-side sessions, requireAuth <br />
[Google Cloud Translation](https://cloud.google.com/translate): used to translate club history and info, plus everything admins create or edit <br />
[Brevo](https://www.brevo.com): used as a transactional email service: inquiry notifications (to club), inquiry replies (to submitter), admin invite links and password reset links <br />
[Cloudflare R2](https://developers.cloudflare.com/r2/): used for storing images and videos

## Runtime (ops) layer
the pipeline: deployed via my shared CI/CD pipeline (see the Deploy repo) <br />
runs on a Raspberry Pi as a 6-container compose stack: db, backend, frontend, dashboard, nginx proxy and cloudflared <br />
[nginx](https://nginx.org): determines which app gets the request based on the URL path <br />
[Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-tunnel/): connects the Pi to the internet outbound-only, so no ports are open <br />
nightly backup: restore-verified `pg_dump` on the Pi, mirrored to my PC

# Styling
My best styling was done on the public site, here are the best styling examples with tools used to create them:
<ul>
  <li><b>News cards</b> on desktop homepage: fully custom; cards' positions are computed in JS and applied as inline CSS 3D transforms; not featured on phones due to its heavy load</li>
  <li><b>The landing div</b> in schedule page: a pair of looping videos stacked in a column on top of which is a static title, styled with custom SCSS using my sass library's colors and spacing</li>
  <li><b>The 'Join Us' div</b> on the homepage: uses 2 stacked videos that crossfade for a seamless loop via custom Svelte logic; the moving text is a custom `typewriter` Svelte action paused off-screen by IntersectionObserver; the flicker is a pure-CSS `::after` pseudo-element blinking on a keyframe</li>
  <li><b>News carousel</b> in news page: my sass library in combination with Svelte</li>
  <li><b>Page transition:</b> a custom full-screen wipe overlay in the root layout using Svelte state and CSS</li>
  <li><b>Animation on scroll:</b> my own `reveal` Svelte action (IntersectionObserver) with CSS transitions</li>
</ul>


# Features
## Public site
<ul>
  <li>homepage with auto-crossfading hero + animated sections</li>
  <li>weekly and monthly training schedule with a swipeable calendar</li>
  <li>full roster with individual archer profile pages</li>
  <li>individual archer profile page contains interactive 3D bow viewer, real achievements, coaches & students</li>
  <li>pages for achievements, sponsors, club history, and contact (membership, sponsor and donation) inquiry forms; the submissions appear in the dashboard and email the club inbox</li>
  <li>fully responsive, with a dedicated mobile pass across every page</li>
  <li>Croatian + English, switchable via a cookie-driven language toggle</li>
</ul>


## Dashboard
<ul>
  <li>receives inquiries sent through contact forms</li>
  <li>manages replies to inquiries from the inbox built into the dashboard, hosts admin invites (72h link) and password-reset (30-min link) pages</li>
  <li>CRUD functionality of most public site data like posts, events, team roster, sponsor descriptions, archer bios, achievement titles, event names, any article text</li>
  <li>CRUD functionality of any and all files in above mentioned sections that have images and videos</li>
  <li>publish workflow for posts, events and archers - draft, published and hidden - which controls their visibility on the public site</li>
  <li>CRUD functionality of events' categories</li>
  <li>used to change passwords</li>
  <li>has an FAQ page</li>
  <li>fully responsive, with a dedicated mobile pass across every page</li>
  <li>some features like each admins' profile data edits, problem & site ideas reporting, admin account management (CRUD of each member) and more only have UI in place; they are planned to be implemented if the website gets adopted by the official club as their new official website due to the amount of effort they require</li>
</ul>
