# Ubuntu Resource Management — Internal Tools

A small static website hosting internal tools for Ubuntu Resource Management consultants. Designed to run on **GitHub Pages** (free) with **Google Drive sync** so consultants can pick up work seamlessly between their phones and laptops.

## What's in this repo

| File | Purpose |
|---|---|
| `index.html` | Landing page — lists the available tools |
| `monthly-service-report_1.html` | The Monthly Service Report Editor app (with Drive sync) |
| `README.md` | This file — deployment instructions |
| `SETUP-GOOGLE-DRIVE.md` | Step-by-step Google Cloud setup to enable Drive sync |

That's it. Pure static HTML. No build step, no Node, no dependencies to install.

---

## Quick deploy — get it online in 10 minutes

### Step 1 · Create a GitHub repository

1. Sign in to **github.com** with your Ubuntu account.
2. Click the **+** in the top right → **New repository**.
3. Name it something like `ubuntu-tools` or `urm-internal` (lowercase, no spaces).
4. Choose **Public** (required for free GitHub Pages — your code is visible, but your *data* never is, because data lives in each consultant's private Google Drive).
5. Tick **Add a README file** so the repo is initialised.
6. Click **Create repository**.

### Step 2 · Upload the files

1. On the new repo's page, click **Add file** → **Upload files**.
2. Drag **`index.html`**, **`monthly-service-report_1.html`**, **`README.md`** and **`SETUP-GOOGLE-DRIVE.md`** into the upload area.
3. Scroll down, leave the commit message as-is, click **Commit changes**.

### Step 3 · Turn on GitHub Pages

1. On the repo page, click **Settings** (top menu, far right).
2. In the left sidebar, click **Pages**.
3. Under **Build and deployment**:
   - **Source**: *Deploy from a branch*
   - **Branch**: *main* / *(root)*
4. Click **Save**.
5. Wait about 1–2 minutes. Refresh the Pages settings page — you'll see a green box with your live URL, something like:
   ```
   https://your-github-username.github.io/ubuntu-tools/
   ```
6. Open that URL in your browser. **You should see the landing page.**

✅ At this point the website is live. The Monthly Service Report Editor already works in **browser-only mode** (data saves locally on each device).

### Step 4 · Enable Google Drive sync

To get the cross-device sync between phone and laptop, follow the separate guide: **[SETUP-GOOGLE-DRIVE.md](./SETUP-GOOGLE-DRIVE.md)**.

You'll do this once. It takes about 15 minutes. Without it, consultants still have a fully working app — they just can't sync between devices.

---

## Updating the site later

When you (or I) make changes:

1. On the repo page, click the file you want to edit.
2. Click the **pencil icon** (top right of the file view).
3. Make changes (or paste in an updated file).
4. Scroll down, click **Commit changes**.
5. Wait ~1 minute. GitHub Pages auto-rebuilds and your URL serves the new version.

Or you can use **Add file → Upload files** again to replace a whole file by dragging the new version on top.

---

## Adding more tools to the site

The landing page (`index.html`) has a placeholder card labelled "More tools on the way". When you want to add another tool:

1. Upload the new HTML file to the repo (e.g. `hr-procedures.html`).
2. In `index.html`, duplicate the existing tool card block and update its title, description, and `href`.
3. Commit. The new tool appears on the landing page within ~1 minute.

---

## Custom domain (optional)

If you want a friendlier URL like `tools.ubuntu-rm.co.za` instead of the long github.io URL:

1. In your domain registrar (e.g. Afrihost, Domains.co.za), add a `CNAME` record pointing `tools` to `your-github-username.github.io`.
2. In the repo's **Settings → Pages**, fill in the **Custom domain** field with `tools.ubuntu-rm.co.za` and tick **Enforce HTTPS**.

> ⚠️ If you set up a custom domain, you'll also need to update the *Authorized JavaScript origins* in Google Cloud (see SETUP-GOOGLE-DRIVE.md, Step 5) to include the custom domain.

---

## What if a consultant runs into trouble?

The app is built to fail gracefully:

| Situation | What happens |
|---|---|
| Consultant has no internet | App still works — data saves to their browser locally. Syncs when back online. |
| Consultant hasn't signed in to Google yet | App still works — data saves to their browser only. They can sign in any time. |
| Drive sync isn't set up at all (CLIENT_ID blank) | Sign-in button is hidden. App works in browser-only mode. |
| Consultant switches phones | They sign in with the same Google account on the new device → data appears automatically. |
| Consultant accidentally deletes the Drive file | The app falls back to whatever's in their browser. Next save creates a new Drive file. |

---

## Tech stack

- **Hosting**: GitHub Pages (static, free)
- **Frontend**: Plain HTML + CSS + JavaScript, no framework
- **Storage**: `localStorage` (instant) + `IndexedDB` (daily snapshots) + Google Drive (cross-device sync)
- **Auth**: Google Identity Services (OAuth 2.0) with `drive.file` scope
- **Fonts**: Fraunces (display), Inter Tight (body), JetBrains Mono (numerics)

---

## Contact

Built for **Ubuntu Resource Management (Pty) Ltd** · Reg No. 2016/211207/07
