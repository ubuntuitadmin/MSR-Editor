# Setting up Firebase for Ubuntu Tools — step by step

This guide gets the central database, sign-in, and admin dashboard working. You'll do this **once**.

**Time required:** ~25–30 minutes
**Cost:** Free (Firebase's Spark plan — generous limits, no credit card)
**Difficulty:** Click-through-the-console. The only code you'll write is pasting one config block and one security-rules block.

> **Before you start**: deploy the website to GitHub Pages first (see `README.md`). You'll need the live URL during this setup.

---

## Step 1 · Create a Firebase project

1. Go to **https://console.firebase.google.com**
2. Sign in with the Ubuntu Google account that will own this project.
3. Click **Add project** (or "Create a project" if it's your first).
4. **Project name**: `Ubuntu Tools` (or anything you like — only you see it). Click **Continue**.
5. **Google Analytics**: you can disable this (we don't need it). Click **Continue**.
6. Click **Create project**. Wait ~30 seconds for setup to finish, then click **Continue**.

✅ You're in the project dashboard.

---

## Step 2 · Enable Google Sign-In

1. In the left sidebar, click **Build → Authentication**.
2. Click **Get started**.
3. On the "Sign-in method" tab, find **Google** in the list of providers. Click it.
4. Toggle **Enable** to on.
5. **Project support email**: pick your email from the dropdown.
6. Click **Save**.

✅ Google sign-in is now active for your project.

---

## Step 3 · Add your GitHub Pages URL to authorised domains

1. Still in **Authentication**, click the **Settings** tab.
2. Scroll down to **Authorized domains**. You'll see `localhost` and `<project>.firebaseapp.com` already listed.
3. Click **Add domain**.
4. Type your GitHub Pages domain (without the `https://`), e.g.:
   ```
   your-username.github.io
   ```
   (Just the hostname — no path, no `https://`, no trailing slash.)
5. Click **Add**.

If you ever add a custom domain like `tools.ubuntu-rm.co.za`, come back here and add it the same way.

---

## Step 4 · Create the Firestore database

1. In the left sidebar, click **Build → Firestore Database**.
2. Click **Create database**.
3. **Choose a location**: pick the closest region. For South Africa, **europe-west1** (Belgium) or **europe-west3** (Frankfurt) give the lowest latency.
   > ⚠️ **You cannot change this later.** Pick once, carefully.
4. Click **Next**.
5. **Security rules**: leave the default for now (we'll replace them in a moment). Pick **Start in production mode**.
6. Click **Create**.

Wait ~30 seconds for the database to provision.

---

## Step 5 · Paste the security rules

This is the critical step that makes the app safe. Without proper rules, anyone could read or write to the database. The rules below ensure:
- Consultants can only read & write their own report data
- Admins can read & write any consultant's data
- Nobody can promote themselves to admin

1. In Firestore, click the **Rules** tab (top of the page).
2. **Delete everything** in the editor.
3. Paste this entire block:

```javascript
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {

    function isAdmin() {
      return request.auth != null
        && exists(/databases/$(database)/documents/admins/$(request.auth.token.email));
    }

    function isOwner(email) {
      return request.auth != null && request.auth.token.email == email;
    }

    match /users/{email} {
      allow read:   if isOwner(email) || isAdmin();
      allow create: if isOwner(email);
      allow update: if isOwner(email) || isAdmin();
      allow delete: if isAdmin();

      match /reports/{month} {
        allow read, write: if isOwner(email) || isAdmin();
      }
    }

    match /admins/{email} {
      allow read:  if isAdmin() || isOwner(email);
      allow write: if isAdmin();
    }

    match /it-reports/{month} {
      allow read, write: if isAdmin();
    }
  }
}
```

4. Click **Publish** at the top.

✅ Rules are live. The database is now locked down.

---

## Step 6 · Get the Firebase config and paste it into the HTML

1. Click the **gear icon ⚙️** at the top of the left sidebar → **Project settings**.
2. Scroll down to **Your apps**. There won't be any apps yet.
3. Click the **`</>`** (web app) icon.
4. **App nickname**: `Ubuntu Tools Web` (anything is fine).
5. Leave **Firebase Hosting** unchecked.
6. Click **Register app**.
7. You'll see a block of code like:

   ```javascript
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "ubuntu-tools.firebaseapp.com",
     projectId: "ubuntu-tools",
     storageBucket: "ubuntu-tools.appspot.com",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abc..."
   };
   ```

8. Copy **just the contents inside the `{ ... }`** (the 6 fields).

9. Open `monthly-service-report.html` in your editor (or GitHub's web editor).

10. Search for `firebaseConfig = {`. You'll find a block near the top:

    ```javascript
    const firebaseConfig = {
      apiKey:            "",
      authDomain:        "",
      // ... etc
    };
    ```

11. Replace the empty values with the ones from Firebase. The end result should look like:

    ```javascript
    const firebaseConfig = {
      apiKey:            "AIzaSy...your-key-here...",
      authDomain:        "ubuntu-tools.firebaseapp.com",
      projectId:         "ubuntu-tools",
      storageBucket:     "ubuntu-tools.appspot.com",
      messagingSenderId: "1234567890",
      appId:             "1:1234567890:web:abc..."
    };
    ```

12. Save the file. Commit it to GitHub.

13. Back in Firebase: click **Continue to console**.

> **Is it OK that the API key is public?** Yes. Unlike a server API key, the Firebase web API key is designed to be embedded in client code. The security comes from the Firestore rules (Step 5), not from secrecy.

---

## Step 7 · Add yourself as the first admin

This is how you tell Firebase "this email address is an admin". You'll do it directly in the Firestore console, because the security rules don't let anyone write to the `admins` collection from inside the app (deliberately — that would be a security hole).

1. In Firebase, click **Firestore Database** in the left sidebar.
2. You should see an empty database. Click **+ Start collection**.
3. **Collection ID**: `admins`. Click **Next**.
4. **Document ID**: **type your email address exactly**, e.g. `ubuntuadmin@gmail.com`.
   > ⚠️ Use the email of the Google account you'll sign into the app with. **Case matters** — type it exactly as it appears.
5. Add one field:
   - Field name: `addedAt`
   - Type: `timestamp`
   - Value: click "Use current value" or pick any date
6. Click **Save**.

✅ You are now an admin.

To add more admins later, just repeat this step with their email address.

---

## Step 8 · Test it

1. Wait ~60 seconds for GitHub Pages to deploy your edited HTML file.
2. Open your GitHub Pages URL in a browser.
3. You should see the **Ubuntu Monthly Service Report sign-in screen** with a "Sign in with Google" button.
4. Click it. Sign in with the Google account you just added as admin.
5. After sign-in, you should see the **Admin Dashboard** with:
   - A month picker at the top
   - 6 summary cards (Consultants, Active this month, Total visits, CCMA, BC, Team kilometres) — all showing 0 or 1
   - A "Consultants" table showing just yourself
6. Click your own row in the consultants table. You should drop into the report editor, with a "← Back to dashboard" button in the sidebar.

🎉 If all of that works, the system is live.

---

## How to use it day-to-day

### As a consultant

1. Open the website
2. Click **Sign in with Google** with your Ubuntu work email
3. You see the editor with your current month's report
4. Use the **Reporting month** dropdown in the sidebar to jump to past or future months
5. Everything you type auto-saves to the cloud

### As an admin

1. Open the website, sign in with your admin email
2. You see the **Admin Dashboard** with all consultants
3. Pick a month from the top picker to see totals for that month
4. Click any consultant's row to open their report — you can view, edit, generate Word reports, anything
5. Click **← Back to dashboard** in the sidebar to return to the team view

### Adding a new consultant

There's nothing to do. The first time they sign in with their Google account, their profile is automatically created and they appear in your admin dashboard.

### Removing a consultant (e.g. they leave)

In **Firestore Database**, navigate to `users → their.email@example.com` and delete the document. Their reports stay in the database for your records but they can't access them anymore. Their next sign-in attempt will still succeed (Google auth), but the next visit will just recreate an empty profile. If you want to completely block them, see "How to remove access" below.

### Promoting someone to admin

In **Firestore Database**, navigate to the `admins` collection. Click **+ Add document**. Document ID = their email. Save. Done.

### Demoting an admin

In `admins`, find their email, click it, click the three-dot menu, **Delete document**.

### How to remove access entirely

1. In Firebase → **Authentication** → **Users** tab
2. Find their email
3. Click the three-dot menu → **Delete account**
4. They can no longer sign in. Their stored data remains in Firestore for your records.

---

## Costs and limits (Firebase Spark plan)

You're well within the free tier. Some rough numbers:

| Resource | Free limit | Your team's usage |
|---|---|---|
| Database reads | 50,000/day | ~few hundred/day |
| Database writes | 20,000/day | ~few hundred/day |
| Storage | 1 GB | <10 MB likely ever |
| Bandwidth | 10 GB/month | <100 MB/month likely |
| Authenticated users | Unlimited | Your consultant count |

You will not exceed any of these. If you somehow do, Firebase will tell you and offer to upgrade (the paid plan is pay-as-you-go and still very cheap).

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Sign-in screen shows "Setup required" | The `firebaseConfig` in the HTML still has blank values. Re-do Step 6. |
| "Sign in with Google" pops up then closes with an error | The GitHub Pages domain isn't in **Authorized domains**. Re-do Step 3. |
| I signed in but see "consultant" instead of "admin" | Your email isn't in the `admins` collection, or the case doesn't match exactly. Re-check Step 7 — the document ID must match your Google sign-in email exactly. |
| Admin dashboard says "Failed to load consultants" | Either the rules weren't published correctly (re-do Step 5) or your email isn't actually in the `admins` collection. |
| A consultant signs in but their data doesn't sync | Check the browser console (F12). Most likely the rules are wrong — re-publish them. |
| I get "Missing or insufficient permissions" | Almost always a rules issue. Go to Firestore → Rules and re-publish the block from Step 5. |

---

## What this setup gives you

✅ Cross-device sync for every consultant (phone, tablet, laptop)
✅ Each consultant's data is private to them — even other consultants can't see it
✅ Admins see everyone, can drill in, edit, and export Word reports
✅ Monthly history preserved forever — go back to any past month
✅ Real-time sync (changes appear on other devices within a second)
✅ Offline-friendly: works without internet, syncs when reconnected
✅ Audit trail: every save records who edited and when

That's a serious team-app foundation. From here you can keep adding tools to the same site without redoing any of this — they can all share the same Firebase project.
