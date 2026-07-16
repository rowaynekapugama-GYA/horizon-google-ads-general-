# Horizon Dental — Google Ads landing pages

Standalone deployment for the Google Ads campaign. No veneers pages, no forms, no serverless functions — conversions are calls and booking-portal clicks only.

| File | Serves at | Ad group |
|---|---|---|
| `index.html` | `/` | Ad Group 1 — General / Dentist Sydney CBD |
| `emergency-dentist-sydney.html` | `/emergency-dentist-sydney` (`.html` depending on host) | Ad Group 2 — Emergency Dental |

Both pages are `noindex`, include the SmileOx call-tracking snippet (numbers wrapped in `.js-phone` spans; the helper mirrors the swapped tracking number into every tel: link), and expose `.js-book-online` / `.js-call` classes for GTM/Google Ads click conversions. All assets are local except the two dentist photos (SmileSearch S3) and the Vimeo hero film.
