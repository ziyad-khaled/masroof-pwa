# Masroof v2 — مصروف 💸
Personal spending tracker PWA — installable on Android & iPhone, backed by Supabase cloud storage.

> **Version history:** v1 = initial tracker · v2 = this release (Supabase sync + editable budgets + smart Quick Add + sinking fund editing + transaction history editing)

---

## What's new in v2

| # | Change |
|---|--------|
| 1 | Budget caps (Needs / Wants / Sinking) are now editable in Settings |
| 2 | Smart Quick Add driven by 6-month frequency × recency scoring |
| 3 | Sinking Fund transactions are fully editable and deletable |
| 4 | Log Contribution form added to Sinking Fund tab |
| 5 | **Supabase cloud sync** — all data backed up automatically, survives browser data clears |

---

## Supabase setup (run once)

### Step 1 — Create a Supabase project
1. Go to **https://supabase.com** → New project
2. Choose a name, password, and region (pick EU / closest to Egypt)
3. Wait ~2 minutes for it to spin up

### Step 2 — Run this SQL in the SQL Editor
Go to your project → **SQL Editor** → paste and run:

```sql
-- expenses
create table expenses (
  id bigint primary key,
  user_id text not null,
  name text not null,
  amount numeric not null,
  cat text,
  cat_label text,
  nw text,
  type text,
  date timestamptz not null,
  created_at timestamptz default now()
);
alter table expenses enable row level security;
create policy "anon access" on expenses using (true) with check (true);

-- sf_log (sinking fund transactions)
create table sf_log (
  id bigint primary key,
  user_id text not null,
  name text,
  amount numeric not null,
  type text,
  date timestamptz not null,
  created_at timestamptz default now()
);
alter table sf_log enable row level security;
create policy "anon access" on sf_log using (true) with check (true);

-- wishlist
create table wishlist (
  id bigint primary key,
  user_id text not null,
  name text not null,
  cost numeric default 0,
  done boolean default false,
  created_at timestamptz default now()
);
alter table wishlist enable row level security;
create policy "anon access" on wishlist using (true) with check (true);

-- app_state (goals, budgets, settings — one row per device)
create table app_state (
  user_id text primary key,
  goal_saved numeric default 0,
  goal_monthly numeric default 6000,
  budget_needs numeric default 3500,
  budget_wants numeric default 2000,
  budget_sinking numeric default 3000,
  cat_mapping jsonb default '{}',
  quick_items jsonb default '[]',
  updated_at timestamptz default now()
);
alter table app_state enable row level security;
create policy "anon access" on app_state using (true) with check (true);
```

### Step 3 — Get your credentials
- **Project URL:** Settings → API → Project URL (looks like `https://xxxx.supabase.co`)
- **Anon key:** Settings → API → `anon` `public` key (long `eyJ…` string)

### Step 4 — Connect in the app
1. Open the app → **⚙️ Settings** → Supabase sync card at the top
2. Paste your Project URL and Anon key
3. Tap **Test** to verify the connection
4. Tap **Save & connect**
5. Tap **⬆ Push local → cloud** to upload any existing local data

---

## How sync works

- **Automatic:** Every expense added, edited, or deleted syncs to Supabase within ~1.2 seconds (debounced)
- **Indicator:** Small dot under the app name — 🟢 synced · 🟡 saving · 🔴 error · ⚫ not connected
- **Manual push:** Settings → "Push local → cloud" — useful first time or after offline use
- **Manual pull:** Settings → "Pull cloud → local" — use when setting up a new device or after browser storage was cleared
- **No login required:** Each device gets a stable anonymous ID stored in localStorage
- **localStorage is primary cache** — app works fully offline; Supabase is the backup layer

---

## All features

### 🏠 Home
- Live Needs / Wants / Sinking Fund budget bars — amber at 80%, red at overflow
- Smart Quick Add: one-tap buttons ranked by 6-month frequency × recency
- Summary stats: entries, cash total, digital total
- Full expense list with type and Needs/Wants badges

### ➕ Add
- Cash / Digital / Sinking Fund type toggle
- Arabic name support (auto right-to-left)
- 16 emoji categories
- Over-budget warning if Wants cap exceeded

### 📊 Stats
- 6-month spending trend chart
- Needs / Wants / Sinking split vs caps
- Category breakdown for current month
- Daily spending chart

### 🐷 Sinking Fund
- Running balance (contributions minus withdrawals, all time)
- Monthly contribution progress bar toward target
- Log contributions manually (with optional note)
- Log withdrawals separately (don't count against Wants budget)
- **Tap any transaction row** → bottom sheet to edit name, amount, date, or delete
- Tap the stat cards to jump to the relevant form
- Wishlist with checkboxes (pre-loaded: shoes, clothes, license, gift, pull-up bar, weights)

### 🎯 Goals
- 608,000 EGP marriage/house goal with % progress bar
- Remaining amount and estimated completion date
- Manual savings balance update (for MNT-Halan / Thndr)
- Savings allocation display: 🪙 Gold 50% / 📈 Stocks 20% / 🏦 Bank 30%

### 📤 Export
- Excel (.xlsx) in your original April/March/May file format
- CSV export
- Full JSON backup and restore

### ⚙️ Settings
- **☁️ Supabase sync** — URL + key input, Test button, Push/Pull, status indicator
- Edit Needs / Wants / Sinking budget caps
- Smart Quick Add preview and manual refresh
- Category → Needs/Wants mapping overrides

---

## Install on your phone

**Android (Chrome):**
1. Open hosted URL in Chrome
2. Banner: "Install app on home screen" → Install
3. Or: Chrome menu ⋮ → "Add to Home Screen"

**iPhone (Safari only):**
1. Open URL in Safari → Share button □↑ → "Add to Home Screen" → Add

---

## How to host (free)

### Netlify Drop (2 minutes, no account needed)
1. Go to **https://app.netlify.com/drop**
2. Unzip and drag the `spending-pwa` folder onto the page
3. Get a free URL → open on phone → install

### GitHub Pages
1. New repo → upload all 6 files → Settings → Pages → Deploy from main

---

## Auto-detect bank SMS on Android (MacroDroid)

1. Install **MacroDroid** from Play Store (free)
2. Trigger: SMS Received from CIB / NBE / Banque Misr
3. Extract amount: regex `EGP ([\d,]+\.?\d*)`
4. Action → open: `https://your-url/?amount={AMOUNT}&name=Digital+Payment`
5. App opens with amount pre-filled in Digital mode, ready to confirm

---

## Recommended next steps

### Right now
- Run the SQL in Supabase SQL Editor
- Connect credentials in Settings → Test → Save → Push
- Install the app on your phone

### After 1 month of real data
- Check Stats page: if Wants consistently hits the cap, adjust it in Settings
- Quick Add chips will reflect your real habits automatically

### Phase 3 — Android automation
- Set up MacroDroid for bank SMS → auto-fills digital payments

### Future features to consider
- **Income tab** — log salary/freelance; unlocks automatic savings rate
- **Month-over-month comparison** — "Spent 340 EGP more on Wants vs last month"
- **Sinking fund forecast** — months until wishlist fully funded at current rate
- **Recurring expenses** — auto-appear each month (Internet, phone bill)
- **Multi-device sync** — share one `user_id` across devices

---

## File structure
```
spending-pwa/
├── index.html      ← Full app (HTML + CSS + JS, single file)
├── sw.js           ← Service worker (offline support)
├── manifest.json   ← PWA install metadata
├── icon-192.svg    ← Home screen icon
├── icon-512.svg    ← Splash screen icon
└── README.md       ← This file
```
