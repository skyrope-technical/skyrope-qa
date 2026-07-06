# Sky Rope Qatar — Go-Live Guide (skyrope.qa)

Repo folder: `skyrope-qa/`  ·  Host: **Cloudflare Pages**  ·  Registrar: **qhost.qa**

Already done for you:
- Clean deploy repo assembled (site + contact-form Function only; internal decks/profiles/brand-kit left out).
- Cloudflare **D1 database created** — name `skyrope-qa`, id `bbfaef68-7c53-4120-b165-fac25e6cb6f1`.
- `.gitignore`, `README.md`, `wrangler.toml` added (D1 id pre-filled).

Still to do: the D1 `leads` **table is NOT created yet** (Step 4b), and everything below.

---

## 1. Prepare the repo on your PC

A broken `.git` folder was left in `skyrope-qa/` (Linux couldn't finish it). Delete it and init fresh. Open **PowerShell** in the folder:

```powershell
cd "C:\Users\welcome\OneDrive\Documents\SkyRope\Skyrope - Qatar\skyrope-qa"
Remove-Item -Recurse -Force .git
git init
git add -A
git commit -m "Initial commit: Sky Rope Qatar site + contact form"
git branch -M main
```

## 2. Create the GitHub repo and push

1. github.com → **New repository** → name `skyrope-qa` → **Private** → *don't* add README/gitignore → Create.
2. Back in PowerShell (replace `YOUR-USERNAME`):

```powershell
git remote add origin https://github.com/YOUR-USERNAME/skyrope-qa.git
git push -u origin main
```

## 3. Connect Cloudflare Pages to the repo

Cloudflare dashboard → **Workers & Pages → Create → Pages → Connect to Git** → pick `skyrope-qa`.
- **Build command:** *(leave empty)*
- **Build output directory:** `/`
- Save & Deploy. You get a `skyrope-qa.pages.dev` URL.

## 4. Bind the database + email key

**4a. Bindings** — Pages project → **Settings → Functions**:
- **D1 database bindings** → Add → Variable name `DB` → database `skyrope-qa`.

Pages project → **Settings → Environment variables → Production**:
- Add `RESEND_API_KEY` → paste your Resend key → **Encrypt**.

**4b. Create the leads table** — Cloudflare → **Workers & Pages → D1 → skyrope-qa → Console**, paste the contents of `schema.sql`, Run. (This is the step I couldn't run for you.)

Then **Deployments → Retry deployment** so bindings take effect.

## 5. Resend email (contact-form notifications)

The Function sends from `noreply@skyropetechnical.com` to `sales@skyropetechnical.com`. In Resend:
- Keep using the **skyropetechnical.com** domain (verify it in Resend if not already), **or**
- Switch to **skyrope.qa** — then edit `functions/api/contact.js` lines with `from:` / `to:` to the new addresses and verify skyrope.qa in Resend.

Until the domain is verified in Resend, leads still save to D1; only the email notification is skipped.

## 6. Point skyrope.qa at Cloudflare (DNS)

`skyrope.qa` is an apex domain, so the clean route is to move DNS to Cloudflare:

1. Cloudflare → **Add a site** → `skyrope.qa` → Free plan. Cloudflare gives you **2 nameservers** (e.g. `xxx.ns.cloudflare.com`).
2. Log in to **my.qhost.qa** → your domain → **Nameservers** → replace qhost's nameservers with the two Cloudflare ones → Save. (.qa propagation: usually 1–24h.)
3. Once active: Pages project → **Custom domains → Set up a domain** → add `skyrope.qa` **and** `www.skyrope.qa`. Cloudflare auto-creates the DNS records and SSL.

> If qhost.qa does **not** allow changing nameservers, tell me — we'll instead keep DNS at qhost and use a CNAME to `skyrope-qa.pages.dev`, which needs a `www` redirect for the apex. Nameserver move is cleaner, so try that first.

## 7. Verify

- `https://skyrope.qa` loads over HTTPS.
- Submit the contact form → success panel.
- D1 → skyrope-qa → Console: `SELECT * FROM leads ORDER BY created_at DESC LIMIT 5;` shows the row.
- Check `sales@skyropetechnical.com` for the notification email.

---

### Reading leads later
D1 → skyrope-qa → Console:
```sql
SELECT id, name, email, service, status, created_at FROM leads ORDER BY created_at DESC LIMIT 50;
```
