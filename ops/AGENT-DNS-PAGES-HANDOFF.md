# Cursor agent handoff — wire bloodmutant.com DNS + GitHub Pages (headless)

Paste into Terminal after `agent login` if needed:

```bash
cd ~
agent "$(cat <<'PROMPT'
GOAL: Make https://bloodmutant.com serve the professional Blood Mutant author site from GitHub Pages (repo rockymountainamerica-bot/bloodmutant-site). Cut Shopify off this domain (currently HTTP 402 Payment Required). Wire Buy CTA already on the site to Amazon — do not change buy links. No ART. No spend. No invent bios/reviews.

LAW
- Site already on main: https://github.com/rockymountainamerica-bot/bloodmutant-site
- Files present: index.html, styles.css, CNAME (bloodmutant.com), README.md, assets/favicon.svg
- Amazon buy URL must remain exactly:
  https://www.amazon.com/dp/B0GCPL9LB5?lv=shuf&channelId=500&plpRedirect=mhFallback
- Founder Mac may already have gh auth; use gh. Do not invent credentials.
- Prefer headless gh / dig / curl. If GoDaddy needs browser login or 2FA, stop and tell Nicholas the exact screen.

STEP 1 — Enable GitHub Pages
- Repo: rockymountainamerica-bot/bloodmutant-site, branch main, path /
- Prefer gh api:
  gh api -X POST repos/rockymountainamerica-bot/bloodmutant-site/pages \
    -f build_type=legacy \
    -f source[branch]=main \
    -f source[path]=/
  If site already exists: PATCH instead (or delete+recreate only if safe).
- Verify preview: https://rockymountainamerica-bot.github.io/bloodmutant-site/
  Expect HTTP 200 and “Blood Mutant” / Buy on Amazon in HTML.

STEP 2 — Custom domain on the repo
- CNAME file already contains: bloodmutant.com — confirm on main; do not wipe it.
- Set Pages custom domain via gh api Pages update for cname=bloodmutant.com.
- Do NOT force HTTPS until DNS resolves to GitHub Pages IPs.

STEP 3 — DNS cutover (headless)
- Discover where DNS for bloodmutant.com lives (whois / dig NS). Likely GoDaddy or leftover Shopify DNS.
- REMOVE Shopify-pointing A/CNAME/ANAME for apex and www that keep 402.
- SET for apex bloodmutant.com (@):
  A 185.199.108.153
  A 185.199.109.153
  A 185.199.110.153
  A 185.199.111.153
  Optional AAAA: 2606:50c0:8000::153 2606:50c0:8001::153 2606:50c0:8002::153 2606:50c0:8003::153
- SET www CNAME → rockymountainamerica-bot.github.io
- Prefer godaddy CLI/API if credentials/session exist on this Mac; else open DNS UI and prepare exact records for one founder tap.
- If registrar needs browser login / 2FA, stop and report — do not invent passwords.

STEP 4 — Verify
- dig +short bloodmutant.com A → the four GitHub IPs (after TTL).
- curl -sI https://rockymountainamerica-bot.github.io/bloodmutant-site/ → 200
- When DNS matches, enable Enforce HTTPS on Pages.
- curl -sI https://bloodmutant.com → 200 HTML author site (not Shopify 402).
- Confirm Buy href still contains amazon.com/dp/B0GCPL9LB5?lv=shuf&channelId=500&plpRedirect=mhFallback

REPORT BACK (to Nicholas / C3i)
- Pages enabled? Preview URL status code.
- DNS provider found.
- Records changed (before/after).
- Blockers needing Nicholas (login/2FA).
- ETA until apex live.
PROMPT
)"
```
