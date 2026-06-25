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
  "version": 1,        // schema version — keep 1 (see "Versioning")
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
| `labelKey` | optional             | Show a built-in **translated** label instead of `label` — see Localized labels. |
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

> **Staged rollout / unknown keys:** if a `tab:`/`screen:` key isn't understood
> by the installed app version, that row is **hidden** (not shown as a dead row).
> So you can safely add a row for a future native screen now — older apps hide
> it, and it lights up automatically once they update. A web (`url`/`external`)
> row works on every version immediately.

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
  "url": "https://yourstore.com/my-account/promotions",
  "auth": true
}
```

The app appends the seller's secure auto-login token to the URL, so your website
logs them in automatically — same mechanism as the seller's web dashboard. No
password typing.

- **Safety lock:** the token is attached **only when the link is on your store's
  own domain** (the app's configured store URL, including its subdomains). If
  `auth:true` points at any other site, the page still opens but **without** the
  token — so the seller's session can never leak to a third-party host.
- Works only with `action: "url"` (in-app). Does nothing on `external`.
- Public pages: leave `auth` out.

---

## 6. Localized labels (`labelKey`)

By default a row shows its `label` exactly as written. If you want a row to
follow the app's **language** automatically, add a `labelKey` from the built-in
set below. When the app recognizes the key it shows the translated text;
otherwise it falls back to `label`.

```json
{ "label": "Orders", "labelKey": "orders", "icon": "bag", "action": "tab:orders" }
```

Built-in keys that translate: `orders`, `products`, `earnings`, `reviews`,
`settings`, `dashboard`, `menu`, `notifications`, `storeSettings`,
`updateInformation`.

> Custom rows (e.g. Promotions, Training, Help) have no built-in translation —
> just use `label`. Always keep a `label` as the fallback even when you set
> `labelKey`. Section titles are not translated yet.

---

## 7. Hiding things temporarily (`enabled`)

```json
{ "label": "Instant Cash", "icon": "wallet", "action": "url", "url": "https://...", "enabled": false }
```

```json
{ "title": "Grow sales", "enabled": false, "items": [] }
```

---

## 8. Common recipes

```json
{ "label": "Advertisement", "icon": "megaphone", "action": "url", "url": "https://yourstore.com/ads", "auth": true }
```

```json
{ "label": "Seller policies", "icon": "info", "action": "external", "url": "https://yoursite.com/policies" }
```

```json
{ "label": "Products", "labelKey": "products", "icon": "box", "action": "tab:products" }
```

```json
{
  "title": "Payments",
  "items": [
    { "label": "Payments", "icon": "wallet", "action": "url", "url": "https://yourstore.com/my-account/payments", "auth": true },
    { "label": "Statements", "icon": "chart", "action": "url", "url": "https://yourstore.com/my-account/statements", "auth": true }
  ]
}
```

---

## 9. Full working example

```json
{
  "version": 1,
  "enable": true,
  "sections": [
    {
      "title": "Manage business",
      "items": [
        { "label": "Orders",         "labelKey": "orders",            "icon": "bag",   "action": "tab:orders" },
        { "label": "Products",       "labelKey": "products",          "icon": "box",   "action": "tab:products" },
        { "label": "Earnings",       "labelKey": "earnings",          "icon": "cash",  "action": "tab:earnings" },
        { "label": "Store settings", "labelKey": "storeSettings",     "icon": "store", "action": "screen:storeSettings" },
        { "label": "Business info",  "labelKey": "updateInformation", "icon": "info",  "action": "screen:updateInfo" }
      ]
    },
    {
      "title": "Grow sales",
      "items": [
        { "label": "Promotions",       "icon": "discount",  "action": "url", "url": "https://yourstore.com/my-account/promotions", "auth": true },
        { "label": "Advertisement",    "icon": "megaphone", "action": "url", "url": "https://yourstore.com/my-account/ads",        "auth": true },
        { "label": "Training",         "icon": "school",    "action": "url", "url": "https://yoursite.com/training" },
        { "label": "How-to tutorials", "icon": "play",      "action": "url", "url": "https://yoursite.com/tutorials" }
      ]
    },
    {
      "title": "Account & help",
      "items": [
        { "label": "Reviews",     "labelKey": "reviews",  "icon": "star", "action": "screen:reviews" },
        { "label": "Settings",    "labelKey": "settings", "icon": "gear", "action": "screen:settings" },
        { "label": "Help center", "icon": "help", "action": "external", "url": "https://yoursite.com/help" }
      ]
    }
  ]
}
```

---

## 10. Rules & gotchas

- **It must be valid JSON** — close every `{ }` / `[ ]`, comma between items, no trailing comma. Check at https://jsonlint.com. A broken file makes the app use its built-in menu.
- **No real comments in the live file** — the `//` notes here are for reading only.
- **Replace every `https://REPLACE-ME/...`** with a real link, or delete that row.
- **`enable: false`** (top level) turns the whole custom menu off.
- **Changes go live on the next Menu-tab open** with internet. The app caches the last good version, so an edit may take one reopen to appear, and works offline afterwards.
- **Don't rename the file or move it off `main`.**

---

## 11. Versioning

`version` tells the app which schema it's reading. This app build understands
**version 1**. If a future app release introduces a breaking schema it will use a
higher number — and **older apps will ignore a file whose version they don't
support** and show their built-in menu instead of mis-reading it. Practically:
leave `version` at `1` unless you're told otherwise.

---

## 12. Where the app points (for developers)

Default raw URL is above. Repoint at runtime via cloud config key
`vendorConfig.ProducerMenuUrl` (no rebuild). New `screen:` / `icon:` / `labelKey`
keywords are added in app code (`producer_menu_screen.dart`).
