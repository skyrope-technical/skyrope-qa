# Sky Rope Qatar — skyrope.qa

Marketing site for Sky Rope Technical Services (Qatar), hosted on **Cloudflare Pages**.

- **Frontend:** `index.html` + `uploads/` (images)
- **Backend:** `functions/api/contact.js` — Cloudflare Pages Function handling the enquiry form (`POST /api/contact`)
- **Database:** Cloudflare D1 (`skyrope-qa`, id `bbfaef68-7c53-4120-b165-fac25e6cb6f1`) — `leads` table
- **Email:** Resend (transactional notification to the sales inbox)

## Bindings required on Cloudflare Pages
| Type | Name | Value |
|------|------|-------|
| D1 database | `DB` | `skyrope-qa` |
| Environment variable (encrypted) | `RESEND_API_KEY` | your Resend API key |

Pages build settings: **Build command:** *(none)* · **Output directory:** `/`

See `DEPLOYMENT.md` for the full step-by-step.
