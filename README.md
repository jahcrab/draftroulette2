# Draft Roulette — deploy notes

Everything the game needs is inside `index.html`. No build step, no framework,
no server. Drop it on any static host and it works.

- 463 KB on disk, ~111 KB gzipped (hosts gzip automatically)
- One external request: Google Fonts. Everything else — 830 team-seasons,
  5,000 simulated drafts, all game logic — is inline.

## 1. Put the game online

**Option A — Cloudflare Pages** (recommended if you want the leaderboard, so
everything lives with one provider)

1. Push this folder to a GitHub repo, or use `npx wrangler pages deploy .`
2. In Cloudflare: Workers & Pages → Create → Pages → connect the repo
3. Build command: leave empty. Output directory: `/`

**Option B — Netlify Drop** (fastest, ~30 seconds)

Go to app.netlify.com/drop and drag this folder in. Done.

**Option C — GitHub Pages**

Push to a repo, then Settings → Pages → deploy from `main` / root.

The file must be named `index.html` for the site root to serve it.

## 2. Turn on the global leaderboard (optional)

The game is fully playable without this — it falls back to a local
leaderboard stored in the visitor's own browser.

```bash
npm install -g wrangler
wrangler login
wrangler kv namespace create SCORES     # copy the id into wrangler.toml
wrangler deploy                          # prints your worker URL
```

Then open `index.html`, find this line near the top of the `<script>` block:

```js
const LEADERBOARD_URL='';
```

and paste your worker URL in:

```js
const LEADERBOARD_URL='https://draft-roulette-leaderboard.YOUR-NAME.workers.dev';
```

Re-upload `index.html`. The results screen will now submit to the shared board.

## 3. Custom domain

Both Cloudflare Pages and Netlify let you attach a domain in their dashboard and
issue an HTTPS certificate automatically. Point the domain's nameservers (or a
CNAME) at the host, and that's the whole job.

## Updating later

Edit or replace `index.html` and re-deploy. Nothing else is stateful except the
KV namespace, which holds the leaderboard and survives redeploys.
