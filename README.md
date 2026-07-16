# Horizon Dental — Google Ads landing pages

Standalone deployment for the Google Ads campaign. The General and Emergency pages convert on calls and booking clicks only; the Veneers page adds an enquiry form that relays to the SmileOx CRM.

| File | Serves at | Ad group |
|---|---|---|
| `index.html` | `/` | Ad Group 1 — General / Dentist Sydney CBD |
| `emergency-dentist-sydney.html` | `/emergency-dentist-sydney` (`.html` depending on host) | Ad Group 2 — Emergency Dental |
| `dental-veneers-sydney.html` | `/dental-veneers-sydney` | Ad Group 3 — Veneers (has an enquiry form) |

Both pages are `noindex`, include the SmileOx call-tracking snippet (numbers wrapped in `.js-phone` spans; the helper mirrors the swapped tracking number into every tel: link), and expose `.js-book-online` / `.js-call` classes for GTM/Google Ads click conversions. All assets are local except the two dentist photos (SmileSearch S3) and the Vimeo hero film.

## Veneers enquiry form → SmileOx
`lead.js` must sit in an **`api/` folder** on Vercel (exposed at `/api/lead`). It emails each submission as a
JSON plain-text body to the **veneers-google-ads** SmileOx intake
(`veneers-google-ads+37e24721-df2f-46eb-a1df-622a05f2903c@intake.smileox.com.au`, overridable via `INTAKE_ADDRESS`).
Payload: firstName, lastName, email, phoneNumber + `source: "veneers-google-ads-landing"` and `submittedAt`.
Env vars: `SMTP2GO_API_KEY` (required), `INTAKE_ADDRESS` / `SMTP_FROM` / `ALLOW_ORIGIN` (optional).
Test after deploy: `GET /api/lead` returns `{"ok":false,"error":"Method not allowed"}` when live; then submit the
form once and confirm the lead appears in the SmileOx pipeline.

SmileOx status IDs for this intake (for `UPDATE_TARGET_STATUS` payloads if ever needed):
New Lead `f27e3346-57cb-4672-97a2-61854ba8e9f6` · Contacted `e607268c-6f05-48f6-8530-1750131c3ca8` ·
Qualified `0b7ed77d-036c-4927-ac79-e2bede849ce0` · Appointment Scheduled `c170e6b4-00b0-4f1e-8441-0ec3320ec932` ·
Converted `83743712-e0db-4c2b-81d4-ddede8ff94a0`

All calls on every page dial the tracking line `+61468045083`, hidden behind "Call now"/"Call us" labels.
