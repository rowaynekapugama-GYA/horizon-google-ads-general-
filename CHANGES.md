# Horizon landing pages: 24 Nov 2026 update

Copy these files into the existing book.horizondental.com.au repo, replacing the old versions, then redeploy.
Do NOT replace the whole repo: index, emergency, veneers and Invisalign pages are unchanged by this update.

- vercel.json: redirects zoom-whitening-sydney-cbd(.html) to the homepage (no Zoom offer under $795), keeps the dentist-sydney-cbd redirect
- new-patient-offer-sydney.html: price now $249 (no "usually $270" comparison), dentists David Kozor and Stewart Beggs only, Zoom link removed from footer
- booking-confirmed.html: Zoom link removed from footer

After deploying, delete zoom-whitening-sydney-cbd.html from the repo.
