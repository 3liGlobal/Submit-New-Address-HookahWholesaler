# Submit-New-Address-HookahWholesaler

> 3LI Global — AIR Global integration estate. Generated 2026-08-31 by the US-Infrastructure audit. Facts below are drawn from this repo's code; anything not directly evidenced is marked _unverified_.

## Connected Resource
- **Azure resource:** none — static HTML/CSS/JS site hosted on **GitHub Pages** (`https://3liglobal.github.io/Submit-New-Address-HookahWholesaler/`).
- **Deploy trigger:** GitHub Pages, built from the `main` branch (no Actions workflow / no Azure Function App in this repo).
- **Talks to:**
  - **Zoho Forms (global / US data centre)** — the form POSTs to `forms.zohopublic.com/airglobal/form/SubmitAddressHookahwholesaler/.../htmlRecords/submit` (portal `airglobal`).
  - **Loqate / Addressy address-capture API** (`api.addressy.com/Capture/Interactive/Find` and `/Retrieve`, `Countries=US`) for address autocomplete — called with an embedded API key.
  - **intl-tel-input** + **google-libphonenumber** (CDN) for international phone entry/validation; jQuery, Font Awesome, Bootstrap from CDNs.

## What It Does
It renders a "New Address" capture form (Email, phone with country selector, and address fields) and submits the entry into a Zoho Form on the `airglobal` portal. As the user types a street address it queries Loqate/Addressy for US address suggestions and, on selection, retrieves and fills the structured address; `js/states.js` provides ISO country→state/city mappings that drive the state/city/zip fields, which are shown or hidden depending on the chosen country.

## Why It Exists
It is the self-hosted "submit a new address" capture form for the **Hookah Wholesaler** brand — the wholesaler counterpart to `Submit-New-Address-Global-Hookah` (portal `airglobal1`). Address autocomplete plus validated international phone numbers raise data quality on the address records that flow into Zoho and downstream to the ERP.

## How It Works
1. Static `index.html` presents the form; `css/form.css` and Bootstrap style it. Phone init uses intl-tel-input + libphonenumber.
2. As the user types the address, `fetchData()` calls the Loqate/Addressy `Find` endpoint (US) for suggestions; selecting a container drills in via `Find?Container=` and a final `Retrieve` fills the structured fields. `js/states.js` maps the country to its states/cities and toggles the `state-dropdown` / `state-text` / `city-text` / `zip-text` fields.
3. On submit, `onSubmit="return zf_ValidateAndSubmit()"` runs `js/validation.js` (mandatory-field + `checktype` validation) and, if valid, POSTs the record to the Zoho Forms permalink on `airglobal`.
- **Operator notes:** input `name` attributes must match the Zoho form fields exactly or values submit empty. The Loqate/Addressy API key is currently hard-coded in `index.html` (should be secured/rotated). Hidden inputs `zf_referrer_name`, `zf_redirect_url`, `zc_gad` control referral tracking, redirect, and Google Ads GCLID.

---
_Environment:_ Production — unprefixed repo (no Staging/Prod marker in name)
_Runtime:_ Static HTML/CSS/JS on GitHub Pages (Zoho form template, jQuery, Bootstrap, intl-tel-input, Loqate/Addressy)
