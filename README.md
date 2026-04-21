# Finance Tracker

A single-page personal finance tracker with per-transaction payment-method
tagging, installable as a PWA on iOS and Android.

**Features**

- Daily expense log with individual transactions (amount, category, payment
  method, note)
- **Fixed expenses** on their own dedicated page (rent, subs, bills) with a
  current-month / annual / average KPI row, plus a **Copy from…** button
  that stamps another month's values onto the current one — amounts do not
  auto-repeat because real bills shift (moving apartments, new plan, etc.),
  so each month stays editable
- Budget limits page, now focused purely on variable-spend caps
- Income tracking with "received to" account
- Debt tracking with auto-payoff (avalanche / snowball) projection
- Credit-card-vs-account payment-method selector **everywhere**
  - Paying with a credit card → the card's debt balance increases
  - Paying from an account → the account's balance decreases
  - Income → selected account's balance increases
  - Debt payment → account's balance decreases AND card's balance decreases
- Savings goals + net-worth KPI
- Smart insights (spending trends, budget overruns, emergency-fund months,
  credit reliance, etc.)
- Offline-ready PWA, data stored in your browser's localStorage
- One-click JSON backup / restore
- Multi-year support (each year has its own data)

### Design

- **All-SVG icon system.** ~50 Lucide-style line icons replace every emoji
  across nav, cards, buttons, modals, and toasts. The icon registry and
  `icon(name, size)` helper live in `index.html`; `<i data-icon="…"></i>`
  placeholders are hydrated automatically after each render and inside
  dynamically-mounted modal content.
- **Custom icon-capable dropdowns.** Native `<option>` elements can't render
  SVG, so the paid-with picker, category picker, account-type picker, and the
  one-time migration modal use a custom `.cs` component — button + popover
  menu with per-option icon, label, and check-mark. Closes on outside click
  or `Escape`, auto-flips upward when near the viewport bottom, and
  auto-scrolls the selected option into view.
- **iOS-safe layout.** Fixed gradient background with `background-attachment`
  + safe-area insets means no Safari top/bottom white bars. A keyboard-aware
  layout lock (`body.kb-lock`) keeps focused inputs above the iOS keyboard
  instead of being covered.
- **Stable tab strip.** The month tabs scroll container auto-centers the
  active tab after each re-render, so late months (Oct–Dec) stay visible
  when tapped (or when the app opens in Q4).

## Running it on your phone

### Option A — GitHub Pages (recommended, works as a real app)

1. Create a new **public** GitHub repo (e.g. `finance-tracker`). You don't
   need to know git deeply for this — use GitHub Desktop or the web UI
   "Upload files" button.
2. Upload these files to the repo root:
   - `index.html`
   - `manifest.json`
   - `sw.js`
   - `icon.svg`, `icon-192.png`, `icon-512.png`, `favicon-32.png`,
     `apple-touch-icon.png`
   - (optional) `README.md`
3. In the repo on github.com, go to **Settings → Pages**. Under *Source*,
   pick **Deploy from a branch**, select `main` and `/ (root)`. Click
   **Save**.
4. Wait ~60s. GitHub shows a green box with your URL, like
   `https://<your-username>.github.io/finance-tracker/`
5. Open that URL on your iPhone in **Safari** (Chrome won't let you install
   PWAs on iOS).
6. Tap the **Share** button → **Add to Home Screen** → **Add**.
7. Launch it from the home screen. It opens fullscreen like a native app,
   works offline, and shows a proper purple `$` icon.

### Option B — Local file, no hosting

- Open `index.html` in any modern browser. Everything works; but **service
  worker + "Add to Home Screen" won't install** unless served over HTTP(S).
- Quick local server on Mac:

```bash
cd ~/Documents/finance-tracker
python3 -m http.server 8080
```

Then visit `http://<your-mac's-LAN-ip>:8080/` on your iPhone Safari (they
must be on the same Wi-Fi). Install as above.

## Updating the app after a change

If you edit `index.html` and want iOS to see the update:

1. Open `sw.js` and bump the `CACHE_VERSION` string
   (e.g. `"ft-v4.1.0"`). The current version is `ft-v4.0.9`.
2. Push/upload again.
3. On iPhone, open the app once while online — the new service worker
   activates; next launch uses the new files.

## Navigation

The bottom nav has eight sections:

| Icon             | View     | What it's for                                     |
| ---------------- | -------- | ------------------------------------------------- |
| `home`           | Home     | Dashboard: KPIs, bar charts, category breakdown   |
| `calendar`       | Daily    | Per-day transaction log (variable expenses)       |
| `lock`           | Fixed    | Recurring fixed expenses (rent, subs, bills)      |
| `target`         | Budget   | Variable-category monthly caps & over-budget warn |
| `wallet`         | Income   | Income entries, which account they land in        |
| `credit-card`    | Debt     | Credit cards + loans, payoff simulator            |
| `gem`            | Wealth   | Accounts, savings goals, net-worth view           |
| `sparkles`       | Insights | Rule-based advisor tips based on your data        |

A `+` floating action button opens **Quick Add** from anywhere for the
fastest "log a purchase" flow. The header's gear icon opens **Settings**
(import / export / restore / sync).

## Importing your old v3 backup

If you had the previous Finance Tracker HTML file and exported a backup:

1. Open the new app → **Settings** (gear icon in the header) → **Import**.
2. Pick your old backup JSON. The app detects the v3 format and walks you
   through a **one-time migration**:
   - You'll be asked which account your past spending came from (default
     Checking). This is only used to tag history — your current account
     and credit-card balances are **not** modified.
3. After migration, the **v3 snapshot** is kept in localStorage.
   Settings → **Restore v3 snapshot** reverts if anything looks wrong.

Automatic migration also happens if you open the new app on a device that
already had the old app's v3 data in localStorage.

## Data privacy

All data lives in your browser's localStorage. Nothing is sent to a
server. The GitHub Pages host only serves static HTML/JS/CSS — it can't
read your numbers. Export a backup periodically and store the JSON
somewhere safe (iCloud Drive, Dropbox, email to yourself).

## Tips

- The floating **+** button is the fastest way to log a purchase — open
  it, type amount, tap category + payment method, save.
- Credit cards show up as payment methods by default. For non-credit debts
  (e.g. "Sister (I owe)"), toggle off *"Usable as payment method"* on the
  Debt screen so they don't appear in the picker.
- Accounts with **Auto-sync off** (see the Wealth screen) keep their
  balance untouched when transactions reference them — useful for e.g. an
  Investments account where you track the balance manually.
- When picking a paid-with / category / account-type, the dropdown shows
  an icon next to each option. Tap outside the menu or press `Esc` to
  close it without changing the selection.

## File layout

```
finance-tracker/
├── index.html             The whole app (HTML + CSS + JS inline)
├── manifest.json          PWA metadata (name, icons, colors)
├── sw.js                  Service worker (offline cache, network-first HTML)
├── icon.svg               Vector icon
├── icon-192.png           Android / PWA icon
├── icon-512.png           Android / PWA icon
├── apple-touch-icon.png   iOS home-screen icon
├── favicon-32.png         Browser-tab favicon
└── README.md              This file
```
