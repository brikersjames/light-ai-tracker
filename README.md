# Light AI (ALGO.NE) Investment Tracker

Single-page tracker for ALGO positions across James's TFSA, James's margin account, Meagan's TFSA, and the MV Capital SPV. The repo itself is the database: trades live in `data/trades.json` and the page commits new trades back via the GitHub API.

## One-time setup (~5 minutes)

1. **Create the repo.** On GitHub: New repository → name it `light-ai-tracker` (public or private*). Upload everything in this folder (`index.html`, `data/`, `.github/`), keeping the folder structure.
2. **Enable GitHub Pages.** Repo → Settings → Pages → Source: "Deploy from a branch" → Branch `main`, folder `/ (root)` → Save. Your tracker will be at `https://<username>.github.io/light-ai-tracker/`.
3. **Create the access token** (lets the page save trades):
   - GitHub → Settings (your avatar) → Developer settings → Personal access tokens → **Fine-grained tokens** → Generate new token.
   - Repository access: **Only select repositories** → `light-ai-tracker`.
   - Permissions → Repository permissions → **Contents: Read and write**. Everything else: none.
   - Expiration: your call (max 1 year; you'll paste a new one when it expires).
4. **Connect the page.** Open the tracker → ⚙ *GitHub sync settings* → enter username, repo, branch `main`, and paste the token → Save & test. The token lives only in that browser (repeat on your phone/other devices).

\* If the repo is **private**, GitHub Pages requires a paid plan. A public repo works free — note trade data in it is public. Alternative: keep the repo private and open `index.html` locally; it still syncs via the API.

## Stock price

The page tries Yahoo Finance live (direct, then two CORS proxies). As a backstop, the included GitHub Action (`.github/workflows/price.yml`) fetches the price every 30 minutes during market hours and commits `data/price.json`. There's also a manual price box. Enable the Action under the repo's Actions tab (it needs the default `GITHUB_TOKEN`, already configured).

## Adding trades

Use the form on the page — it commits to `data/trades.json`. Without a token configured, trades save in the browser only (a yellow banner reminds you) and sync automatically once you set the token. You can also edit `data/trades.json` by hand; format:

```json
{"id":"t-1","date":"2026-07-03","account":"james-margin","action":"buy","quantity":10000,"price":0.30,"net":-3000,"source":"manual"}
```

`net` is negative for buys, positive for sells (matches your Questrade export).

## What the numbers mean

- **Avg cost / book value** — average-cost (ACB) method per account, as CRA expects.
- **Realized P&L** — sale proceeds minus ACB of shares sold.
- **SPV** — your $353,500 (incl. 1% fee) = 7% of the $5M SPV → 2,800,000 as-converted shares ($0.125 conversion) + 2,800,000 warrants at $0.25. Warrant value is net of the $700,000 exercise cost and counted only when the stock is above $0.25. SPV value is shown net of the 2% distribution fee. Footnote shows the debenture floor (principal + 12% compounding quarterly) as the downside case.
