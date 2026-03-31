# Back Order System

A private, hosted back order management tool — single HTML file, zero dependencies, deployable to GitHub Pages in minutes.

---

## Quick Start (GitHub Pages)

### 1. Create a GitHub repository
1. Go to [github.com](https://github.com) and sign in
2. Click **New repository**
3. Name it (e.g. `backorder-system`)
4. Set visibility to **Private** (recommended for internal tools)
5. Click **Create repository**

### 2. Upload the file
1. On your new repo page, click **Add file → Upload files**
2. Drag and drop `index.html`
3. Click **Commit changes**

### 3. Enable GitHub Pages
1. Go to **Settings → Pages**
2. Under *Source*, select **Deploy from a branch**
3. Set branch to `main`, folder to `/ (root)`
4. Click **Save**
5. After ~60 seconds, your URL will appear:
   `https://YOUR-USERNAME.github.io/backorder-system/`

> **Private repos on GitHub Pages**: GitHub Pages for private repos requires a **GitHub Pro, Team, or Enterprise** account. If you're on a free plan, either make the repo public (the login screen still protects access) or use an alternative host like Netlify or Cloudflare Pages (both free, both support private source repos).

---

## Configuration

All configuration is at the **top of the `<script>` block** in `index.html`. Open the file in any text editor.

### Adding / changing users

```js
var USERS = {
  'admin':      'backorder2026',   // username: password
  'purchasing': 'purchase99',
  'warehouse':  'warehouse1'
};
```

Add a new line for each user. Remove lines to remove access.

### Changing the Edit Mode PIN

```js
var EDIT_PIN = '1234';
```

Change `'1234'` to any 4-digit number.

---

## Connecting a Real Backend

The app is built around a clean **API abstraction layer** — four functions at the top of the script:

```js
var API = {
  getOrders:   function() { ... },
  createOrder: function(order) { ... },
  updateOrder: function(id, patch) { ... },
  deleteOrder: function(id) { ... }
};
```

**Currently:** uses `localStorage` (data is per-browser, not shared between users).

**To share data between users**, replace the function bodies with `fetch()` calls to your backend. The rest of the app does not need to change. Example swap for a REST API:

```js
var API_BASE = 'https://your-api.example.com';

var API = {
  getOrders: function() {
    return fetch(API_BASE + '/orders').then(function(r) { return r.json(); });
  },
  createOrder: function(order) {
    return fetch(API_BASE + '/orders', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(order)
    }).then(function(r) { return r.json(); });
  },
  updateOrder: function(id, patch) {
    return fetch(API_BASE + '/orders/' + id, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(patch)
    }).then(function(r) { return r.json(); });
  },
  deleteOrder: function(id) {
    return fetch(API_BASE + '/orders/' + id, { method: 'DELETE' });
  }
};
```

### Recommended free backends
| Option | Notes |
|--------|-------|
| **Firebase Firestore** | Free tier, real-time, easy setup |
| **Supabase** | Postgres-based, free tier, REST API built-in |
| **Airtable** | Spreadsheet UI + REST API, free tier |
| **PocketBase** | Self-hosted, single binary, very simple |

---

## Features Summary

| Feature | Details |
|---------|---------|
| Login screen | Username + password, configurable in the script |
| View back orders | Card layout with search and status filter |
| New back order | Multi-line product table with validation |
| Status management | TO BE ORDERED → ORDERED → COMPLETED |
| Edit mode | PIN-protected, required for status changes & deletes |
| Mobile friendly | Responsive layout, works on phones and tablets |
| Zero dependencies | No npm, no build step, single HTML file |
| API-ready | Swap 4 functions to connect any backend |

---

## File Structure

```
/
└── index.html      ← The entire application (HTML + CSS + JS)
└── README.md       ← This file
```

---

## Security Notes

- The user login is a **lightweight access gate** — passwords are stored in the HTML file in plain text. Anyone who can view the source of the HTML file can see them.
- For a **private GitHub repo**, the source is not publicly visible.
- For stronger security, move authentication to a real backend with hashed passwords and session tokens.
- The Edit PIN is similarly a convenience lock, not cryptographic protection.
