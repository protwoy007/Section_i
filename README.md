# Section Notice Board — Setup

## 1. Create a Firebase project (free)
1. Go to https://console.firebase.google.com → **Add project** → name it anything (e.g. `section-notices`).
2. In the project, click the **</>** (web) icon to register a web app. Copy the `firebaseConfig` object it gives you.
3. Paste those values into `firebase-config.js`, replacing the placeholders.

## 2. Turn on Authentication
1. In Firebase console → **Build → Authentication → Get started**.
2. Enable **Email/Password** sign-in method.
3. Under **Users**, manually add your 2 admin accounts (email + password) — do NOT use public sign-up.
4. In `firebase-config.js`, set `ADMIN_EMAILS` to those exact 2 emails.

## 3. Turn on Firestore (the database)
1. Firebase console → **Build → Firestore Database → Create database** → start in **production mode**.
2. Go to the **Rules** tab and paste:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /notices/{noticeId} {
      allow read: if true;
      allow write: if request.auth != null &&
        request.auth.token.email in ['admin1@example.com', 'admin2@example.com'];
    }
  }
}
```
Replace the two emails with your real admin emails (must match `ADMIN_EMAILS` in the config file), then **Publish**.

## 4. Test locally
Just open `index.html` in a browser — Firestore works over the internet even from a local file. Go to `admin.html`, log in with one of the admin accounts, and add a notice. It should appear on `index.html` instantly (even in another browser/tab, no refresh needed).

## 5. Publish on GitHub Pages
1. Create a new GitHub repo (e.g. `section-notices`), push these files (`index.html`, `admin.html`, `style.css`, `firebase-config.js`) to it.
2. Repo → **Settings → Pages** → Source: `main` branch, `/ (root)` → Save.
3. Your site will be live at `https://<your-username>.github.io/section-notices/`.

## Notes
- Your Firebase **API key being visible in the JS is normal and safe** — Firebase security comes from the Firestore rules above (which check the logged-in user's email), not from hiding the key.
- The 50 members of your section just open the GitHub Pages link — no login needed, they only see the read-only board.
- To add a 3rd admin later, just add their email to Firebase Auth users **and** to `ADMIN_EMAILS` + the Firestore rules.
