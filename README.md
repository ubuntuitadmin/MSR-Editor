# Ubuntu Resource Management — Internal Tools

A small static website hosting internal tools for Ubuntu Resource Management consultants and admins. Designed to run on **GitHub Pages** (free) with a **Firebase backend** so consultants can work seamlessly across devices and admins can oversee the whole team.

## What's in this repo

| File | Purpose |
|---|---|
| `index.html` | Landing page — lists the available tools |
| `monthly-service-report.html` | The Monthly Service Report Editor app (Firebase-backed) |
| `README.md` | This file — deployment instructions |
| `SETUP-FIREBASE.md` | Step-by-step Firebase setup (one-time, ~25 minutes) |

That's it. Pure static HTML. No build step, no Node, no dependencies to install.

## Architecture (in one paragraph)

The site is hosted on GitHub Pages as plain HTML/JS. Authentication uses **Firebase Auth** (Google sign-in). Data is stored in **Firestore**, with each consultant's monthly reports under `/users/{email}/reports/{YYYY-MM}`. A small **`admins`** collection lists which users are admins; admins see a team dashboard and can drill into any consultant's report. Security is enforced by **Firestore security rules** so consultants can only access their own data while admins can access everyone's.

---

## Quick deploy — get it online in 10 minutes

### Step 1 · Update the GitHub repository

If you already deployed the previous Drive-sync version of this app, **just replace the existing files** in your `ubuntu-tools` repo:
- Replace `monthly-service-report.html` with the new one
- Replace `README.md` with this file
- Delete `SETUP-GOOGLE-DRIVE.md`
- Add `SETUP-FIREBASE.md`

If you're deploying fresh:
1. Sign in to github.com → **+ → New repository** → name `ubuntu-tools` (Public).
2. Tick "Add a README file" and create.
3. **Add file → Upload files** — drag all four files in. Commit.

### Step 2 · Turn on GitHub Pages (skip if already enabled)

1. Repo **Settings → Pages**.
2. **Source**: *Deploy from a branch*. **Branch**: *main* / *(root)*. **Save**.
3. Wait ~1–2 minutes. Your live URL appears at the top of the Pages settings, e.g. `https://your-username.github.io/ubuntu-tools/`.

### Step 3 · Set up Firebase

Follow **[SETUP-FIREBASE.md](./SETUP-FIREBASE.md)** start-to-finish. This is the most important step. Without it, the app shows a "Setup required" screen.

Once you've pasted the Firebase config into `monthly-service-report.html` and added your email to the `admins` collection in the Firestore console, you're done.

---

## Updating the site later

Edit the file on github.com (pencil icon on the file view) or upload a new version via **Add file → Upload files**. GitHub Pages auto-redeploys in ~60 seconds.

---

## Adding more tools

The landing page (`index.html`) has a placeholder card for future tools. To add one:

1. Upload the new HTML file (e.g. `hr-procedures.html`).
2. In `index.html`, duplicate an existing tool card block and update its title, description, and `href`.
3. Commit.

If the new tool needs cloud sync, it can re-use the same Firebase project — just include the same `firebaseConfig` snippet at the top of the new file.

---

## Custom domain (optional)

If you want `tools.ubuntu-rm.co.za` instead of the github.io URL:

1. In your domain registrar, add a `CNAME` record: `tools` → `your-github-username.github.io`.
2. In the repo's **Settings → Pages**, set **Custom domain** to `tools.ubuntu-rm.co.za`, tick **Enforce HTTPS**.
3. In Firebase: **Authentication → Settings → Authorized domains** — add `tools.ubuntu-rm.co.za`.

---

## Day-to-day cheat sheet

| Task | Where to do it |
|---|---|
| Add a new consultant | Nothing — they just sign in with their Google email; their profile auto-creates |
| Promote someone to admin | Firebase → Firestore → `admins` collection → Add document, ID = their email |
| Remove someone's access | Firebase → Authentication → Users → Delete their account |
| Change Firestore security rules | Firebase → Firestore → Rules tab |
| Update the app | Edit files on GitHub |

---

## Costs

GitHub Pages: free. Firebase: free at your team's usage level (well within the Spark plan limits). No credit card needed.

---

## Tech stack

- **Hosting**: GitHub Pages (static, free)
- **Frontend**: Plain HTML + CSS + JavaScript, no framework
- **Auth**: Firebase Authentication (Google provider)
- **Database**: Cloud Firestore (real-time, NoSQL)
- **Offline support**: `localStorage` mirror + auto-sync on reconnect
- **Fonts**: Fraunces (display), Inter Tight (body), JetBrains Mono (numerics)

---

## Contact

Built for **Ubuntu Resource Management (Pty) Ltd** · Reg No. 2016/211207/07
