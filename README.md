# reface CLOTHING — CMS setup (Netlify + Decap CMS)

## What changed
- `index.html` — your page, now reading text/products/videos from `/content/*.json`. The old fake admin (`admin / admin123`) and client-side editor were **removed**. The HTML text is kept as a fallback if the JSON can't load.
- `content/site.json`, `content/products.json` — everything editable.
- `admin/index.html`, `admin/config.yml` — the CMS at `yoursite.com/admin`.
- `netlify.toml` — security headers, `noindex` for `/admin`.
- `images/uploads/` — where uploaded images land.

## Setup (≈15 minutes)
1. **GitHub repo.** Create a repo (private is fine), upload everything in this folder to branch `main`.
2. **Netlify.** Add new site → Import from Git → pick the repo. Build command: *(empty)*. Publish directory: `.`
   (If the site already exists on Netlify, link it to this repo instead: Site configuration → Build & deploy → Continuous deployment.)
3. **GitHub OAuth app** (this is the secure login).
   GitHub → Settings → Developer settings → OAuth Apps → New OAuth App
   - Homepage URL: your site URL
   - Authorization callback URL: `https://api.netlify.com/auth/done`
   - Create it, then generate a **Client secret**.
4. **Netlify OAuth provider.** Site configuration → Access & security → OAuth → Install provider → GitHub → paste Client ID + Client secret.
5. **Edit `admin/config.yml`:** set `repo:` (`username/repo`), `site_url`/`display_url`, and uncomment `site_domain:` if you use a custom domain. Commit.
6. Open `https://yoursite/admin` → **Login with GitHub** → edit → **Publish**. Each publish is a Git commit; Netlify redeploys in about a minute.

## Giving clients access
Add them as **collaborators with Write access** on the GitHub repo (they need a free GitHub account). Anyone else is refused at login. To remove access, remove them from the repo.

## Security checklist
- Turn on **2FA** for every GitHub account that can write to the repo.
- Never put the OAuth client secret in the repo; it lives only in Netlify.
- For clients, consider uncommenting `publish_mode: editorial_workflow` in `config.yml` so edits are reviewed before going live (creates pull requests).
- Optional: GitHub branch protection on `main`.
- If the old version (with `admin123`) is live, deploy this one right away.

## Local testing (optional)
`npx decap-server` in this folder, then serve the folder (`npx serve .`) and open `/admin`. No login needed locally.

## Good to know
- New products: leave **ID** blank. The page assigns one. Don't change IDs of existing products.
- Prices are in INR; the page converts to USD itself.
- Search engines see the original product cards in the HTML; the live grid is filled in by JavaScript from the JSON.
- Cart, checkout, and customer accounts on the page are front-end demos, with no payment processing or user database behind them.
- Editing is form-based, not click-on-the-page. If you later want true inline editing, that means moving to a framework (e.g. Astro + TinaCMS).
- Decap is loaded from unpkg (`decap-cms@^3`). To freeze a version, replace `^3.0.0` with an exact version in `admin/index.html`.
