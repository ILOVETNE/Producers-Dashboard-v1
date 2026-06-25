# Producers Dashboard — Menu configuration

This repository holds **`producer_menu.json`**, the file that builds the **Menu**
tab in the Traditional NorthEast **producer (seller) app**.

Edit this file → save → the change appears in the app the **next time the Menu
tab is opened** (and the app has internet). No app update, no Play Store
release. If you ever break the file or the seller is offline, the app quietly
falls back to a built-in menu, so nothing crashes.

The app reads this exact file:

```
https://raw.githubusercontent.com/ILOVETNE/Producers-Dashboard-v1/refs/heads/main/producer_menu.json
```

> Keep the file name `producer_menu.json` and keep it on the `main` branch, or
> the app won't find it.

---

## 1. How the file is structured

```jsonc
{
  "version": 1,        // keep as 1
  "enable": true,      // false = ignore this file, app shows its built-in menu
  "sections": [        // the grey-headed groups, top to bottom
    {
      "title": "Manage business",   // section header text
      "enabled": true,              // optional, false = hide whole section
      "items": [                    // the rows inside this section
        { "label": "Orders", "icon": "bag", "action": "tab:orders" }
      ]
    }
  ]
}
```

### Row (item) fields

| Field      | Required?            | What it does |
|------------|----------------------|--------------|
| `label`    | yes                  | The text shown on the row. |
| `icon`     | recommended          | Picks the row icon — see Icons. |
| `action`   | yes                  | What happens on tap — see Actions. |
| `url`      | only for `url` / `external` | The web address to open. |
| `auth`     | optional (`url` only)| `true` opens the page already logged in — see Authentication. |
| `enabled`  | optional             | `false` hides the row without deleting it. |

---

## 2. The 4 things a row can do (quick view)

| You want the row to…                         | Use |
|----------------------------------------------|-----|
| Jump to one of the app's bottom tabs         | `"action": "tab:..."` |
| Open one of the app's built-in screens       | `"action": "screen:..."` |
| Open a web page **inside** the app           | `"action": "url"` + `"url": "..."` |
| Open a web page in the **phone's browser**   | `"action": "external"` + `"url": "..."` |

---

## 3. Actions

### `tab:` — switch to a bottom-nav tab

| Action            | Goes to        |
|-------------------|----------------|
| `tab:home`        | Home / Dashboard |
| `tab:orders`      | Orders tab     |
| `tab:products`    | Products tab   |
| `tab:earnings`    | Earnings tab   |

```json
{ "label": "Orders", "icon": "bag", "action": "tab:orders" }
```

### `screen:` — open a built-in app screen

| Action                  | Opens             |
|-------------------------|-------------------|
| `screen:reviews`        | Product reviews   |
| `screen:settings`       | Settings          |
| `screen:storeSettings`  | Store settings    |
| `screen:updateInfo`     | Business / profile info |

```json
{ "label": "Reviews", "icon": "star", "action": "screen:reviews" }
```

> Want a brand-new native screen (e.g. a real "Promotions" screen)? That needs a
> small app code change to add the new `screen:` key. Until then, point the row
> at a web page with `url`. A `screen:` key the app doesn't know does nothing when tapped.

### `url` — open a web page inside the app

```json
{ "label": "Training", "icon": "school", "action": "url", "url": "https://yoursite.com/training" }
```

Add `"auth": true` if the page needs the seller logged in — see Authentication.

### `external` — open in the phone's browser

```json
{ "label": "Help center", "icon": "help", "action": "external", "url": "https://yoursite.com/help" }
```

> `auth` is ignored for `external` rows — the phone's browser does not share the
> app's login. Use `url` (in-app) when the page must be logged in.

---

## 4. Icons

Set `"icon"` to one of these. An unknown keyword just shows a neutral dot.

| Keyword | Looks like | | Keyword | Looks like |
|---|---|---|---|---|
| `bag` | shopping bag | | `gear` | settings |
| `box` | product box | | `help` | question mark |
| `cash` | money | | `bell` | notifications |
| `store` | storefront | | `truck` | shipping |
| `info` | info circle | | `wallet` | wallet |
| `discount` | price tag | | `chart` | bar chart |
| `megaphone` | announcement | | `school` | training |
| `play` | play (video) | | `star` | star / reviews |

---

## 5. Authentication (`auth`)

For pages that should open with the seller **already logged in**, use
`"action": "url"` **and** `"auth": true`:

```json
{
  "label": "Promotions",
  "icon": "discount",
  "action": "url",
  "url": "https://yoursite.com/my-account/promotions",
  "auth": true
}
```

The app appends the seller's secure auto-login token to the URL, so your website
logs them in automatically — same mechanism as the seller's web dashboard. No
password typing.

- Use `auth: true` only on **your own** site pages that accept the auto-login token.
- Works only with `action: "url"` (in-app). Does nothing on `external`.
- Public pages: leave `auth` out.

---

## 6. Hiding things temporarily (`enabled`)

```json
{ "label": "Instant Cash", "icon": "wallet", "action": "url", "url": "https://...", "enabled": false }
```

```json
{ "title": "Grow sales", "enabled": false, "items": [] }
```

---

## 7. Common recipes

```json
{ "label": "Advertisement", "icon": "megaphone", "action": "url", "url": "https://yoursite.com/ads", "auth": true }
```

```json
{ "label": "Seller policies", "icon": "info", "action": "external", "url": "https://yoursite.com/policies" }
```

```json
{ "label": "Products", "icon": "box", "action": "tab:products" }
```

```json
{
  "title": "Payments",
  "items": [
    { "label": "Payments", "icon": "wallet", "action": "url", "url": "https://yoursite.com/my-account/payments", "auth": true },
    { "label": "Statements", "icon": "chart", "action": "url", "url": "https://yoursite.com/my-account/statements", "auth": true }
  ]
}
```

---

## 8. Full working example

```json
{
  "version": 1,
  "enable": true,
  "sections": [
    {
      "title": "Manage business",
      "items": [
        { "label": "Orders",         "icon": "bag",   "action": "tab:orders" },
        { "label": "Products",       "icon": "box",   "action": "tab:products" },
        { "label": "Earnings",       "icon": "cash",  "action": "tab:earnings" },
        { "label": "Store settings", "icon": "store", "action": "screen:storeSettings" },
        { "label": "Business info",  "icon": "info",  "action": "screen:updateInfo" }
      ]
    },
    {
      "title": "Grow sales",
      "items": [
        { "label": "Promotions",       "icon": "discount",  "action": "url", "url": "https://yoursite.com/my-account/promotions", "auth": true },
        { "label": "Advertisement",    "icon": "megaphone", "action": "url", "url": "https://yoursite.com/my-account/ads",        "auth": true },
        { "label": "Training",         "icon": "school",    "action": "url", "url": "https://yoursite.com/training",              "auth": false },
        { "label": "How-to tutorials", "icon": "play",      "action": "url", "url": "https://yoursite.com/tutorials",             "auth": false }
      ]
    },
    {
      "title": "Account & help",
      "items": [
        { "label": "Reviews",     "icon": "star", "action": "screen:reviews" },
        { "label": "Settings",    "icon": "gear", "action": "screen:settings" },
        { "label": "Help center", "icon": "help", "action": "external", "url": "https://yoursite.com/help" }
      ]
    }
  ]
}
```

---

## 9. Rules & gotchas

- **It must be valid JSON** — close every `{ }` / `[ ]`, comma between items, no trailing comma. Check at https://jsonlint.com. A broken file makes the app use its built-in menu.
- **No real comments in the live file** — the `//` notes here are for reading only.
- **Replace every `https://REPLACE-ME/...`** with a real link, or delete that row.
- **`enable: false`** (top level) turns the whole custom menu off.
- **Changes go live on the next Menu-tab open** with internet. The app caches the last good version, so an edit may take one reopen to appear, and works offline afterwards.
- **Don't rename the file or move it off `main`.**

---

## 10. Where the app points (for developers)

Default raw URL is above. Repoint at runtime via cloud config key
`vendorConfig.ProducerMenuUrl` (no rebuild). New `screen:` / `icon:` keywords are
added in app code (`producer_menu_screen.dart`).
