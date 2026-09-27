# GetItDone SA

A real, deployable task-marketplace app: users sign up, post tasks, and pay a
connection fee (via PayFast) to unlock contact details. Revenue flows into
your PayFast merchant account, which PayFast pays out to your bank account.

## What's actually here
- `server.js` — Express API: accounts, tasks, PayFast checkout + webhook, admin summary
- `payfast.js` — PayFast signature generation and payment verification
- `public/` — the website (browse/post tasks, sign up, admin dashboard)
- SQLite database file (created automatically) — swap for Postgres later if you outgrow it

## 1. Run it locally first
```bash
npm install
cp .env.example .env
npm start
```
Open http://localhost:3000. The `.env.example` file already contains PayFast's
official **sandbox** test credentials, so you can test the full payment flow
—including a fake "payment"—before any real money or real account is involved.

## 2. Get a real PayFast account (this part only you can do)
1. Go to https://www.payfast.co.za and register as a merchant. You'll need
   your ID number and South African bank account details — this is PayFast
   verifying who they're paying out to (standard KYC).
2. Once approved, get your **Merchant ID** and **Merchant Key** from
   Settings → Integration in your PayFast dashboard, and set a **Passphrase**
   there too (used to sign requests securely).
3. Put those three values into your `.env` file, and set `PAYFAST_MODE=live`
   only once you've fully tested in sandbox.

**This is also how you "withdraw" money.** PayFast isn't a wallet you pull
from manually — once your bank account is linked and verified, PayFast pays
out your balance to your bank on its normal schedule (check your PayFast
dashboard for the exact payout frequency and any minimum threshold). Your
`/admin.html` dashboard shows what's accumulated; your bank account is where
it lands.

## 3. Deploy it so other people can actually use it
This needs to run on a public server, not your own laptop. Easiest options:
- **Render.com** (free tier available): New → Web Service → connect this
  folder/repo → Build command `npm install` → Start command `npm start` →
  add your `.env` values under Environment.
- **Railway.app**: similar one-click deploy from a GitHub repo.

Once deployed, you'll get a URL like `https://getitdone-sa.onrender.com`.
Set `PUBLIC_URL` in your environment variables to that exact URL — PayFast
needs it to know where to send users back after paying and where to send
payment notifications.

## 4. Point PayFast at your live site
In your PayFast dashboard under Integration settings, make sure ITN
(Instant Transaction Notification) is enabled — the app's `notify_url`
(`/api/payfast/notify`) is how PayFast tells your server a payment actually
succeeded, which is what unlocks the contact details.

## 5. Admin dashboard
Visit `/admin.html` on your deployed site and log in with the `ADMIN_KEY`
you set in `.env`. Change this from the example value before going live.

## Before accepting real payments
- Register the business itself (CIPC) — PayFast will likely ask for this too
- Add a Terms of Service and a POPI Act–compliant privacy policy (I can draft
  these if you'd like)
- Test the entire flow in sandbox mode end-to-end at least once

## 6. Letting people "download" the app in South Africa
This is now a real installable app (PWA) — no app store needed:
- **Android/Chrome**: visiting the site shows an "Install App" button; tapping
  it adds a proper icon to the home screen that opens full-screen, like any
  other app.
- **iPhone/Safari**: open the site → Share button → "Add to Home Screen".
  (iOS doesn't support the one-tap install prompt, so this step has to be
  told to users — worth a line on your landing page.)

**If you want it listed in the Google Play Store / Apple App Store proper**,
the code here can be wrapped into a native app using
[Capacitor](https://capacitorjs.com) with almost no changes. You'll need:
- A Google Play Console account (once-off $25) and/or Apple Developer
  Program account ($99/year, requires a registered business for a company
  account)
- App store listing assets (screenshots, description, privacy policy — the
  POPI-compliant one mentioned below covers this)
- To pass store review (roughly a few days for Google, up to a couple of
  weeks for Apple)

Say the word and I'll set up the Capacitor wrapper and Play Store /
App Store submission checklist next.

## Legitimate ways to grow task listings
Don't scrape other platforms (Gumtree, Facebook Marketplace, OLX) — it
generally breaks their terms of service and carries legal risk. Better paths:
encourage direct posting, seed it yourself with real local tasks early on, or
pursue an official data partnership/API with a platform that offers one.
