# Horizon Dental — Google Ads landing pages

Standalone deployment for the Google Ads campaign. The General and Emergency pages convert on calls and booking clicks only; the Veneers page adds an enquiry form that relays to the SmileOx CRM.

| File | Serves at | Ad group |
|---|---|---|
| `index.html` | `/` | Ad Group 1 — General / Dentist Sydney CBD |
| `emergency-dentist-sydney.html` | `/emergency-dentist-sydney` (`.html` depending on host) | Ad Group 2 — Emergency Dental |
| `dental-veneers-sydney.html` | `/dental-veneers-sydney` | Ad Group 3 — Veneers (has an enquiry form) |
| `invisalign-sydney.html` | `/invisalign-sydney` | Ad Group 4 — Orthodontics & Invisalign (has an enquiry form) |
| `new-patient-offer-sydney.html` | `/new-patient-offer-sydney` | $199 new patient offer (Praktika booking with deposit) |
| `zoom-whitening-sydney-cbd.html` | `/zoom-whitening-sydney-cbd` | $499 new patient Zoom whitening offer (Praktika booking with deposit) |
| `booking-confirmed.html` | `/booking-confirmed.html` | Praktika "Successful Booking Redirection URL", fires the booking conversion |

Both pages are `noindex`, include the SmileOx call-tracking snippet (numbers wrapped in `.js-phone` spans; the helper mirrors the swapped tracking number into every tel: link), and expose `.js-book-online` / `.js-call` classes for GTM/Google Ads click conversions. All assets are local except the two dentist photos (SmileSearch S3) and the Vimeo hero film.

## Veneers enquiry form → SmileOx
`lead.js` must sit in an **`api/` folder** on Vercel (exposed at `/api/lead`). It emails each submission as a
JSON plain-text body to the **veneers-google-ads** SmileOx intake
(`veneers-google-ads+37e24721-df2f-46eb-a1df-622a05f2903c@intake.smileox.com.au`, overridable via `INTAKE_ADDRESS`).
Both forms post here. Payloads: firstName, lastName, email, phoneNumber, `submittedAt`, plus per page —
veneers: `smileGoals`, `timeframe`, `source: "veneers-google-ads-landing"`;
invisalign: `alignmentConcerns`, `timeframe`, `source: "invisalign-google-ads-landing"`.
Routing is built in: Invisalign/ortho submissions go to the **ortho-google-ads-landing** intake
(`ortho-google-ads-landing+6309f405-faf0-424c-9903-6b94a1549b63@intake.smileox.com.au`);
veneers submissions go to the **veneers-google-ads** intake. `INTAKE_ADDRESS` (veneers) and
`INTAKE_ADDRESS_INVISALIGN` (ortho) override either without a code change.
The SMTP2GO API key is baked into `lead.js`, so no env vars are required — `SMTP2GO_API_KEY`, `INTAKE_ADDRESS`, `INTAKE_ADDRESS_INVISALIGN`, `SMTP_FROM` and `ALLOW_ORIGIN` all remain available as optional overrides. Only the HTTP API key is used; the SMTP username/password are not needed by these relays.
Test after deploy: `GET /api/lead` returns `{"ok":false,"error":"Method not allowed"}` when live; then submit the
form once and confirm the lead appears in the SmileOx pipeline.

SmileOx status IDs for this intake (for `UPDATE_TARGET_STATUS` payloads if ever needed):
New Lead `f27e3346-57cb-4672-97a2-61854ba8e9f6` · Contacted `e607268c-6f05-48f6-8530-1750131c3ca8` ·
Qualified `0b7ed77d-036c-4927-ac79-e2bede849ce0` · Appointment Scheduled `c170e6b4-00b0-4f1e-8441-0ec3320ec932` ·
Converted `83743712-e0db-4c2b-81d4-ddede8ff94a0`

SmileOx status IDs for the **ortho-google-ads-landing** intake (each intake has its own set):
New Lead `cf932698-092b-4f44-88bc-d39c6d24e687` · Contacted `b08ce235-cd14-4d9f-851d-1757e892a2b3` ·
Qualified `cba9d5f5-0fd7-4357-ac95-5d2da675e7ff` · Appointment Scheduled `5bb93a54-2d57-4692-854a-a7689e319afd` ·
Converted `82468da4-99ec-475e-bfe3-4611055a03fb`

All calls on every page dial the tracking line `+61468045083`, hidden behind "Call now"/"Call us" labels.

## Offer pages and booking conversion (Nov 2026)
- The $199 and $499 pages and booking-confirmed.html carry the same GTM, Google tag, call conversion and Vimeo hero film as the other pages, plus the Meta Pixel `27515145741502546` (Horizon Landing Pixel).
- Praktika redirect URL: `https://book.horizondental.com.au/booking-confirmed.html` (delay 3 seconds, booking request ID ticked). Praktika holds GTM-KX47H9PZ, GA4 G-NJXLRE6VZT, the Meta Pixel and (once created) the Google Ads booking label.
- Booking conversions for Meta (Pixel 27515145741502546) and Google Ads (conversion ID 10985216486 + the booking label) are fired by Praktika itself, entered in Praktika's settings. booking-confirmed.html only pushes the dataLayer event `praktika_booking_confirmed` (booking_id / transaction_id) and a Meta PageView, so nothing double counts. Leave `AW_BOOKING_LABEL` blank in this page while Praktika holds the label.
- Book online clicks on the offer pages push `book_online_click` with `offer` = `new_patient_199` / `zoom_whitening_499`.
- `vercel.json` redirects the retired `dentist-sydney-cbd` page to `/`.
