# Setting up Google Drive sync — step by step

This is the one-time configuration that lets consultants sign in with Google and have their reports automatically sync between phone, tablet, and laptop. Once it's done, you never touch this again unless you want to publish under a custom domain.

**Time required:** ~15 minutes
**Cost:** Free (well within Google's free tier — your usage will be a tiny fraction of it)
**Difficulty:** Click-through. No code beyond pasting one string at the end.

> **Before you start**: deploy the site to GitHub Pages first (see `README.md`). You'll need the live URL (e.g. `https://your-username.github.io/ubuntu-tools/`) to complete Step 5 below.

---

## Step 1 · Open Google Cloud Console and create a project

1. Go to **https://console.cloud.google.com**
2. Sign in with the Ubuntu Google account you want to own this project (most companies use a "main" or "admin" Google account for this — not a personal one).
3. At the very top of the page, click the **project dropdown** (it will say "Select a project" or show the name of your last project).
4. In the dialog that opens, click **New Project** (top right).
5. Fill in:
   - **Project name**: `Ubuntu Tools` (or whatever you want — only you see this)
   - **Organization** / **Location**: leave default
6. Click **Create**.
7. Wait ~30 seconds. A notification will pop up saying the project is ready. Click it (or use the project dropdown) to **switch to the new project**.

✅ You should now see your project name at the top of the page.

---

## Step 2 · Enable the Google Drive API

1. In the search bar at the top of the page, type **Google Drive API** and press Enter.
2. Click the first result: **Google Drive API** (with a yellow folder icon).
3. Click the blue **Enable** button.
4. Wait ~10 seconds. The page will reload showing the Drive API dashboard.

✅ The Drive API is now active for this project. No further config needed here.

---

## Step 3 · Configure the OAuth consent screen

This is what consultants see the first time they click "Sign in with Google".

1. In the left sidebar, click **APIs & Services** → **OAuth consent screen**.
   (If the sidebar isn't showing, click the ☰ icon in the top-left.)
2. Choose **User Type**:
   - Pick **External**
   - Click **Create**
3. Fill in the **App information** page:
   - **App name**: `Ubuntu Monthly Service Report`
   - **User support email**: your Ubuntu email
   - **App logo** (optional): you can upload the Ubuntu logo later
   - Scroll down to **Developer contact information** → enter your email
   - Click **Save and Continue**
4. **Scopes** page:
   - Click **Add or Remove Scopes**.
   - In the search box that appears, type `drive.file` and press Enter.
   - You'll see one result: `.../auth/drive.file` — *"See, edit, create, and delete only the specific Google Drive files you use with this app"*.
   - Tick its checkbox.
   - Click **Update** at the bottom of the panel.
   - Click **Save and Continue**.
5. **Test users** page:
   - You can skip this — we'll publish the app in the next step, which removes the test-user restriction.
   - Click **Save and Continue**.
6. **Summary** page:
   - Review (looks fine), click **Back to Dashboard**.

---

## Step 4 · Publish the app

Without this step, the app only works for up to 100 manually-added test users, and tokens expire after 7 days. Publishing fixes both.

1. Still on the **OAuth consent screen** page, you'll see a section showing **Publishing status: Testing**.
2. Click **Publish App**.
3. A dialog asks: *"Push to production?"* → click **Confirm**.
4. The status now reads **Publishing status: In production**.

> **Will Google require a verification review?**
> No, not for what we're doing. The `drive.file` scope is classed as *non-sensitive* by Google because the app can only see files it created itself. Apps using only non-sensitive scopes are published instantly without review. If you ever added a more permissive scope (like `drive` or `drive.readonly`), Google would require a verification process — but we don't need those.

---

## Step 5 · Create an OAuth Client ID

This is the actual identifier you'll paste into the HTML file.

1. In the left sidebar, click **APIs & Services** → **Credentials**.
2. Near the top of the page, click **+ Create Credentials** → **OAuth client ID**.
3. **Application type**: select **Web application**.
4. **Name**: `Ubuntu Tools Web Client` (only you see this)
5. **Authorized JavaScript origins** — this is the important part:
   - Click **+ Add URI**.
   - Type your GitHub Pages URL **without a trailing slash and without any path**, e.g.:
     ```
     https://your-github-username.github.io
     ```
   - ⚠️ Note: only the *origin* — not the full path to the file. Just the protocol + domain.
   - If you'll use a custom domain later, add it as a second origin (e.g. `https://tools.ubuntu-rm.co.za`).
   - For local testing, you can also add `http://localhost:8080` here.
6. **Authorized redirect URIs**: leave blank. (We use the token flow, not the redirect flow.)
7. Click **Create**.
8. A dialog pops up with your **Client ID** and Client Secret.
   - **Copy the Client ID.** It looks like `123456789012-aBcDeFgHiJkLmNoPqRsTuVwXyZ.apps.googleusercontent.com`.
   - You do **not** need the Client Secret for this app (we're browser-only).
9. Click **OK**.

✅ You now have your Client ID copied to clipboard.

---

## Step 6 · Paste the Client ID into the HTML file

1. On your computer (or in GitHub's web editor), open **`monthly-service-report.html`**.
2. Find the line that says:
   ```js
   const GOOGLE_CLIENT_ID = ''; // e.g. '123456789-abc...apps.googleusercontent.com'
   ```
   (It's near the top of the JavaScript section, just after `const STORAGE_KEY = 'ubuntu_msr_v3';`. If you have a search function: search for `GOOGLE_CLIENT_ID`.)
3. Paste your Client ID between the single quotes:
   ```js
   const GOOGLE_CLIENT_ID = '123456789012-aBcDeFgHiJkLmNoPqRsTuVwXyZ.apps.googleusercontent.com';
   ```
4. Save the file.
5. Commit the change to GitHub (drag and drop the updated file onto the repo, or edit in-browser).

---

## Step 7 · Test it

1. Wait ~1 minute for GitHub Pages to redeploy.
2. Open your GitHub Pages URL on your phone.
3. Open the Monthly Service Report editor.
4. You should see a **"Sign in with Google"** pill in the top toolbar (next to the existing save status pill).
5. Tap it. The Google sign-in screen appears.
6. After signing in, you'll see a permissions screen:
   > *"Ubuntu Monthly Service Report wants to access your Google Account: See, edit, create, and delete only the specific Google Drive files you use with this app."*
7. Click **Allow** (or **Continue**).
8. Back on the editor, the pill should now show your email and a green **"synced"** indicator.
9. Type something into a field. Wait 2 seconds.
10. On your laptop, open the same URL, sign in with the same Google account.
11. Within a couple of seconds, what you typed on your phone appears on your laptop.

🎉 Done.

---

## What consultants do (the short version)

You'll roll this out to your consultants with three steps:

1. Open the website URL on your phone.
2. Tap **"Sign in with Google"** in the top right.
3. Tap **"Allow"** when Google asks for permission.

That's it. From then on, all their reports auto-sync to their own Google Drive across every device they sign in on.

---

## Privacy & security notes (good to be able to answer if asked)

- **Where does my data live?** In each consultant's *own* Google Drive, in a single file named `ubuntu-monthly-service-report.json`. Nobody else — not Ubuntu, not me, not Google's other users — can see it.
- **What can the app access?** Only that one file. The `drive.file` scope means Google literally won't return any other file when the app asks. Personal photos, work documents, anything else in the consultant's Drive is invisible to the app.
- **Can I revoke access?** Yes — any consultant can go to **myaccount.google.com → Security → Third-party apps** and revoke "Ubuntu Monthly Service Report" at any time. Their local browser data is unaffected; only the sync stops.
- **Where's the data stored on the phone/laptop?** Browser localStorage and IndexedDB, plus the Drive copy. All three stay in sync.
- **What if a consultant loses their phone?** Their data is safe — it's in their Drive, not on the phone. They sign in on a new phone, data appears.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Sign-in pill doesn't appear | Check that you pasted the Client ID into `GOOGLE_CLIENT_ID` and re-uploaded the file. Open the browser console (right-click → Inspect → Console tab) and look for errors. |
| `Error 400: redirect_uri_mismatch` or similar | The GitHub Pages URL doesn't match what's in **Authorized JavaScript origins**. Go back to **Step 5** and check the origin exactly matches (no trailing slash, correct subdomain). |
| `Error 403: access_denied` after publishing | Make sure the OAuth consent screen is set to **In production** (Step 4), not still in Testing. |
| Sign-in works but sync says "Error" | Open the console (F12 → Console). Most likely the Drive API isn't enabled (re-check Step 2) or the scope is wrong (re-check Step 3, must include `drive.file`). |
| Sync works on one device but not another | The consultant signed in with a different Google account. Each account has its own data file. |
| Need to add a custom domain later | Add the new domain as a second entry in **Authorized JavaScript origins** in Credentials (Step 5). |

---

## What this setup *doesn't* do

Just to be clear on the scope:

- ❌ It doesn't let multiple consultants edit the *same* report at once. Each consultant has their own private file.
- ❌ It doesn't let Ubuntu admins see consultants' data centrally. (If you want that, you'd need a backend like Firebase — happy to add it later.)
- ❌ It doesn't work without internet *on first sign-in*. Once signed in, the app works offline and syncs when reconnected.

Everything else (cross-device sync, offline editing, automatic saving, conflict resolution by timestamp) is built in.
