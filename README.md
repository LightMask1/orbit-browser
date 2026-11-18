# Orbit

A personal web proxy inspired by TungTung's tabbed browser interface. Built for Cloudflare Pages with an integrated Bare v3 HTTP/WebSocket transport and Scramjet 1.1.0 page rewriting. All browser engine assets are served from your own deployment; no public third-party relay is required.

## Features

- Up to 12 independently switchable browser tabs.
- Address and search bars, back/forward navigation, home, and reload.
- DuckDuckGo search by default; Google and Bing are selectable.
- Bookmarks, editable shortcuts, and accent colors saved locally in the browser.
- HTTP(S), streamed responses, uploads, redirects, cookies, and WebSocket relay.
- Responsive layout and keyboard access. Press `/` to focus search and Ctrl/Cmd+Shift+L to focus the address bar.
- No application server browsing-history database. The host may process operational logs and destination sites may record requests.
- Public-web destinations only; IP literals, local hostnames, self-proxying, and nonstandard ports are rejected. Browser requests from other origins are rejected.

## Run locally

Install a recent Node.js version, then:

```sh
npm ci
npm run check
npm run build
npm run dev
```

Open http://localhost:4173. Service workers require HTTPS in production (localhost is allowed for development). Use an up-to-date desktop browser with ServiceWorker and SharedWorker support.

## Deploy on Cloudflare Pages

Build the project and upload the **contents of `dist`** in Cloudflare Pages → Create project → Direct Upload. Include `_worker.js`, `_routes.json`, `_headers`, all assets, and their directory structure. The `orbit-cloudflare.zip` delivered with this project is ready to upload. Do not deploy this as a static-only site: the Worker handles `/bare/*` and `/health`.

No environment variables, API keys, KV database, or third-party proxy endpoints are needed. Alternatively, authenticated Wrangler can deploy with `npx wrangler pages deploy dist --project-name orbitgo`.

## Practical limits

The proxy domain itself must be reachable on the network where you use it. It does not bypass captive-portal sign-in, device controls, or a block on this domain. It is a web proxy, not a VPN or anonymity service. Some sites reject datacenter requests; Google may present an anti-bot check. Protected video, camera/microphone calls, and some authentication flows may not work. YouTube playback is not guaranteed. Use only on networks where you have permission, and avoid entering sensitive credentials into proxy pages.

Cloudflare request quotas apply. The deployment is accessible to anyone who knows its URL; robots directives discourage indexing but do not provide authentication. Access controls and stronger abuse limits can be added if you intend to share it widely. Static resources do not invoke the Worker because `_routes.json` restricts Worker execution to the relay and health endpoints.

## Verification

The initial version was checked with browser-based loading of Example Domain, Wikipedia (including page resources), and DuckDuckGo search results; tab switching, bookmarks, and navigation; and direct transport checks for upstream POST, a real WebSocket echo, invalid destination rejection, and cross-origin rejection. These checks do not establish compatibility with every website or a particular filtered network. Production verification is recorded separately in the delivered deployment notes.

## License and source

Orbit is distributed under AGPL-3.0-only; see `LICENSE`. Unmodified third-party distributions retain their respective licenses, reproduced at `/licenses/` in the deployed site. Source for this application is downloadable at `/source.zip`. Exact dependency versions are pinned in `package-lock.json`.

- Scramjet 1.1.0: https://github.com/MercuryWorkshop/scramjet/tree/57ba89e
- Bare Mux 2.1.9: https://github.com/MercuryWorkshop/bare-mux
- Bare transport module 2.2.5: https://github.com/MercuryWorkshop/bare-transport

The stable Scramjet 1.x release uses the older Bare Mux transport interface. Upgrade Scramjet, the controller, and transport packages together if moving to the experimental Scramjet 2.x generation.
