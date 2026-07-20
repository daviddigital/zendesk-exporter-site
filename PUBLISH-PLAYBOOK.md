# Publish Playbook — Zendesk Exporter for Google Sheets

Everything you need to list the add-on on the Google Workspace Marketplace, in order.
**Legend:** ✅ = I already did it, no action. 🧑 = you, in a browser (only these need you).

Google's publishing surface has no API — steps marked 🧑 are unavoidably manual clicks. I've pre-filled every value so they're copy-paste-upload. Do the 🧑 steps **while you still have the laptop**; the review waits afterward don't need you.

---

## ✅ Already done for you
- v1.0 built, tested against your live sandbox, and pushed to the script.
- **Versioned deployment created** — this is what the Marketplace SDK links to.
- All **listing assets** generated (icons, banner, screenshots) — `zendesk-exporter/publish/assets/`.
- **Privacy policy, Terms, and a homepage** written *and hosted live* (URLs below).
- **Listing text** written to fit Google's limits — `zendesk-exporter/publish/listing-copy.md`.

## Reference values (you'll paste these repeatedly)

| Field | Value |
|---|---|
| App name (must be identical everywhere) | `Zendesk Exporter for Google Sheets` |
| Publisher | Cody Labs |
| Support email | davidwatts11@gmail.com |
| **Apps Script — Script ID** | `1ZQRud04YHU96H0eNCIAQ5-l4tAiwSMHN99JtWgWJt-Mip4RG5Zq4rRrA` |
| **Deployment version number** | `1` |
| Homepage URL | https://daviddigital.github.io/zendesk-exporter-site/ |
| Privacy policy URL | https://daviddigital.github.io/zendesk-exporter-site/privacy.html |
| Terms of service URL | https://daviddigital.github.io/zendesk-exporter-site/terms.html |
| Category | Reports & analytics |
| Pricing | Free with paid features (A$15/mo Pro, billed AUD) |

**OAuth scopes (all four — paste each into the consent screen and the SDK):**
```
https://www.googleapis.com/auth/spreadsheets.currentonly
https://www.googleapis.com/auth/script.container.ui
https://www.googleapis.com/auth/script.external_request
https://www.googleapis.com/auth/script.scriptapp
```

**Assets (upload these exact files):**
| Purpose | File | Size |
|---|---|---|
| App icon (required) | `publish/assets/icon-128.png` | 128×128 |
| App icon small (required) | `publish/assets/icon-32.png` | 32×32 |
| OAuth consent logo | `publish/assets/icon-120.png` | 120×120 |
| Card banner (required) | `publish/assets/banner-220x140.png` | 220×140 |
| Screenshot 1 | `publish/assets/screenshot-shot1.png` | 1280×800 |
| Screenshot 2 | `publish/assets/screenshot-shot2.png` | 1280×800 |

---

## 🧑 Step 1 — Gumroad: create the paid product (do this FIRST)
You chose paid-from-day-one, so the license gate needs a real Gumroad product to check against.

1. Sign in at **gumroad.com** → **Products → New product**.
2. Type: **Membership**, name **Zendesk Exporter — Pro**, price **A$15/month**.
3. In the product's **Settings / Checkout**, turn ON **"Generate a unique license key per sale."**
4. Publish the product.
5. Set the product's **URL / permalink** to `zendesk-exporter` (in the product's Settings, the URL field → `https://codylabs.gumroad.com/l/zendesk-exporter`). Use this same value as `LICENSE_PRODUCT_ID` in Step 2 — the add-on accepts either the permalink or Gumroad's internal product_id, so the permalink (easy to find, it's right in the URL) is simplest.

> How licensing works: a buyer gets a license key, pastes it into the add-on's sidebar footer, and the add-on verifies it against Gumroad's `/v2/licenses/verify` using this product ID. Refunds/cancellations automatically lose Pro. The **free tier (1 query, manual refresh) still works without any key** — that's your install funnel.

## 🧑 Step 2 — Arm the license gate (Apps Script Script Properties)
1. Open the script editor: https://script.google.com/d/1ZQRud04YHU96H0eNCIAQ5-l4tAiwSMHN99JtWgWJt-Mip4RG5Zq4rRrA/edit
2. Left sidebar → **Project Settings (gear)** → scroll to **Script Properties → Add script property**. Add three:

| Property | Value |
|---|---|
| `LICENSE_ENFORCED` | `true` |
| `LICENSE_PROVIDER` | `gumroad` |
| `LICENSE_PRODUCT_ID` | *(the product's URL permalink from Step 1, e.g. `zendesk-exporter` — or Gumroad's product_id; either works)* |

Save. The gate is now live: free = 1 query/manual; Pro features require a valid key. (Leave `LICENSE_ENFORCED` unset/`false` if you ever want to reopen everything.)

## 🧑 Step 3 — Create a Google Cloud project
1. Go to **console.cloud.google.com** (same Google account, davidwatts11@gmail.com).
2. Project picker (top bar) → **New Project** → name it **Cody Labs — Zendesk Exporter** → Create.
3. Note the **Project number** (Dashboard → Project info) — you need it in Step 5.

## 🧑 Step 4 — Configure the OAuth consent screen
In that project: **APIs & Services → OAuth consent screen**.
1. User type: **External** → Create.
2. App information:
   - App name: **`Zendesk Exporter for Google Sheets`** (must match exactly)
   - User support email: **davidwatts11@gmail.com**
   - App logo: upload **`publish/assets/icon-120.png`**
3. App domain:
   - Application home page: **https://daviddigital.github.io/zendesk-exporter-site/**
   - Privacy policy link: **…/privacy.html**
   - Terms of service link: **…/terms.html**
4. Developer contact email: **davidwatts11@gmail.com** → Save & continue.
5. **Scopes → Add or remove scopes** → paste the four scopes above → Update → Save & continue.
6. Leave it in **Testing** for now (you can add your own account as a test user to click through without the warning). You'll move to Production + submit for verification in Step 7.

**Per-scope justifications** (you'll need these at verification — copy from `publish/oauth-scope-justifications.md`).

## 🧑 Step 5 — Link the Cloud project to the Apps Script
1. Script editor → **Project Settings (gear) → Google Cloud Platform (GCP) Project → Change project**.
2. Paste the **Project number** from Step 3 → **Set project**.

## 🧑 Step 6 — Enable & configure the Marketplace SDK
1. In the Cloud project: **APIs & Services → Library** → search **"Google Workspace Marketplace SDK"** → **Enable**. (Enable the *SDK*, not the "API".)
2. Go to the SDK → **App Configuration**:
   - App visibility: **Public** ⚠️ *(this locks once saved — Public is what you want for a discoverable listing).*
   - Installation settings: **Individual + admin install** (default).
   - App integration: check **Editor add-on (Docs, Sheets, Slides, Forms)**.
     - **Extension → Sheets.** Script ID: `1ZQRud04YHU96H0eNCIAQ5-l4tAiwSMHN99JtWgWJt-Mip4RG5Zq4rRrA`, **Version: `1`**.
   - OAuth scopes: paste the four scopes.
   - Developer links: privacy + terms URLs (above). → **Save**.
3. Go to the SDK → **Store Listing**:
   - Application name, short description, detailed description, category → copy from `publish/listing-copy.md`.
   - Graphics: upload icon-128, icon-32, banner-220x140, and both screenshots (see asset table).
   - Support links: Privacy, Terms (URLs above); Support = `mailto:davidwatts11@gmail.com`.
   - Language: English. → **Save**.

## 🧑 Step 7 — Submit (two review tracks) + the domain gotcha
1. **Marketplace listing review:** from the SDK Store Listing, **Publish**. Google reviews the listing (usually days).
2. **OAuth verification:** OAuth consent screen → **Publish app** → **Prepare for verification** → submit (needs the scope justifications + a short demo video of the consent grant and each scope in use; 1–3 weeks).

⚠️ **Domain gotcha (verification only):** OAuth verification requires the homepage/privacy URLs to be on a **domain you own and can verify in Google Search Console**. `github.io` is GitHub's domain, so it can't be verified as yours. To pass verification you'll need to renew **codylabs.io** (it lapsed) or use another domain you control, host these same pages there, verify it in Search Console, and swap the URLs in Steps 4 & 6. The github.io URLs are fine for **submitting** and for testing under the 100-user cap — just not for final verification.

## After approval
- The listing goes live; installs start. Reply in the Zendesk community/threads with the link (drafts in `gtm-01-zendesk-exporter.md`).
- Iterating on code later: `clasp push` then `clasp deploy` to cut a new version, and bump the version number in the SDK App Configuration.

---

### The only things that truly need *you* (all in-browser, all today):
1. Gumroad product (Step 1) → gives you `LICENSE_PRODUCT_ID`.
2. Paste 3 Script Properties (Step 2).
3. GCP project + OAuth consent + link + Marketplace SDK config + submit (Steps 3–7).
Everything else — code, deployment, assets, legal pages, hosting — is done.
