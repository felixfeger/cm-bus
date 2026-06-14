# cm-bus
# City Metro Bus System — Deployment Guide

## What you're deploying
- **Cloudflare Worker** (`worker/index.js`) — API backend
- **Cloudflare Pages** (`pages/public/`) — Customer site + Admin portal
- **D1 Database** — All route, stop, bus, and arrival data

---

## Step 1 — Create D1 Database

1. Cloudflare Dashboard → **Workers & Pages → D1**
2. Click **Create database** → Name it `city-metro-bus`
3. Open the database → **Console** tab
4. Paste the entire contents of `schema.sql` and click **Execute**

---

## Step 2 — Deploy the Worker

1. Dashboard → **Workers & Pages → Create → Worker**
2. Name it `citymetro-bus-api`
3. Click **Edit Code**, paste the full contents of `worker/index.js`
4. Click **Deploy**

### Bind D1 to the Worker
1. Go to your Worker → **Settings → Bindings**
2. Click **Add → D1 Database**
   - Variable name: `BUS_DB`
   - Select: `city-metro-bus`
3. Save

### Set the Admin Secret
1. Worker Settings → **Variables → Add variable**
   - Name: `ADMIN_KEY`
   - Value: (choose a strong secret password)
   - Click **Encrypt** ✓
2. Save and redeploy

### Note your Worker URL
It will look like: `https://citymetro-bus-api.YOUR-SUBDOMAIN.workers.dev`

---

## Step 3 — Update the API URL in all files

Find and replace `https://YOUR_WORKER.workers.dev` in these 4 files:
- `pages/public/index.html`
- `pages/public/admin.html`
- `pages/public/pages/arrivals.html`
- `pages/public/pages/trip-planner.html`

Replace with your actual Worker URL from Step 2.

---

## Step 4 — Deploy Pages site

1. Dashboard → **Workers & Pages → Create → Pages**
2. Choose **Upload assets**
3. Name: `citymetro-bus`
4. Upload the entire contents of `pages/public/` (drag the folder)
5. Deploy

### Add your logo
Place `logo.png` in `pages/public/` before uploading. It will appear in the nav and footer.

---

## File structure of Pages upload

```
pages/public/
├── index.html          ← Homepage
├── admin.html          ← Admin portal
├── logo.png            ← Your logo (add this)
├── assets/
│   └── style.css       ← Shared stylesheet
└── pages/
    ├── arrivals.html   ← Live arrivals
    └── trip-planner.html
```

---

## Step 5 — Connect a custom domain (optional)

1. Pages → your project → **Custom domains**
2. Add e.g. `bus.citymetro.xyz`
3. Add the CNAME in Cloudflare DNS (auto-filled if same account)

---

## Using the Admin Portal

1. Go to `your-pages-url/admin.html`
2. Click **Sign In** → enter your `ADMIN_KEY`
3. **Create a route** in the Routes tab
4. Select it from the dropdown
5. Click **Draw Route** tool → click the canvas to lay waypoints
6. Right-click or click **Save Route** when done
7. Click **Place Stop** → click on the route to add stops
8. In the stop editor, add rail connections (line + station name)
9. Click **Place Bus** → click on the route to add a bus
10. Set bus speed in the Buses tab panel

---

## How bus ETAs work

- Buses have an (x, y) canvas position and a speed in mph
- 1 pixel = 1 mile on the canvas (1:1 scale)
- ETA to a stop = distance (pixels) ÷ speed (mph) × 60 minutes
- The customer site and arrivals board auto-refresh every 15–30 seconds
- Move buses in the admin by dragging them on the canvas

---

## Rail line colors (for reference)

| Line | Color     |
|------|-----------|
| A    | #0057A8   |
| B    | #CC0000   |
| D    | #7B2D8B   |
| E    | #F5A623   |
| F    | #FF6B00   |
| K    | #E91E8C   |
