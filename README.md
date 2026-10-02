# Just Us — a private two-person chat website

A single `index.html` file. No build step, no server to run — host it anywhere static (GitHub Pages, Netlify, Vercel). Messages sync live between two people using a free Firebase database.

## 1. Create a free Firebase project (~3 minutes)

1. Go to https://console.firebase.google.com and click **Add project**. Give it any name, skip Google Analytics.
2. In the left sidebar, go to **Build → Firestore Database → Create database**. Choose **Start in test mode** for now (you'll tighten this in step 3), pick any region.
3. Go to **Project settings** (gear icon, top left) → scroll to **Your apps** → click the `</>` (web) icon → register an app (any nickname, no need for hosting).
4. Firebase shows you a `firebaseConfig` object with six values (`apiKey`, `authDomain`, etc). Copy them.

## 2. Fill in the config

Open `index.html` and find this block near the bottom:

```js
const firebaseConfig = {
  apiKey: "PASTE_YOUR_API_KEY",
  authDomain: "PASTE_YOUR_PROJECT.firebaseapp.com",
  ...
};

const ROOM_ID = "change-me-to-a-long-random-code";
```

- Paste your six real values in.
- Change `ROOM_ID` to a long, random, hard-to-guess string (e.g. `luna-cascade-8271-velvet`). This is what keeps your thread separate from anyone else who might use the same Firebase project.

## 3. Lock down the database

By default "test mode" allows anyone to read/write. Before sharing the link, go to **Firestore Database → Rules** and paste:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /rooms/{roomId} {
      allow read, write: if roomId == "PASTE_YOUR_ROOM_ID_HERE";
    }
  }
}
```

Replace `PASTE_YOUR_ROOM_ID_HERE` with the same `ROOM_ID` you set in step 2, then click **Publish**.

**Important honesty note:** this keeps out random strangers, but it isn't bank-grade security — anyone who somehow got both your `ROOM_ID` and Firebase config could read the thread, since there's no login system. For a couple's private chat this is a normal, reasonable trade-off (that's how most small personal projects like this work), but don't reuse this pattern for anything sensitive. Keeping your GitHub repo **private** adds another real layer, since then the config isn't publicly visible at all.

## 4. Put it on GitHub Pages

1. Create a new repository, upload `index.html` to it.
2. Go to the repo's **Settings → Pages**, set **Source** to your main branch, root folder.
3. GitHub gives you a URL like `https://yourname.github.io/repo-name/` — that's the link to send her.

## 5. Using it

The first time each of you opens the link, it asks for a name (stored only on that device). After that, messages just sync live — refresh isn't even needed.

## Customizing

- Colors, fonts, and bubble styling are all in the `<style>` block at the top of `index.html` — plain CSS variables.
- `MAX_MESSAGES` (in the script) caps history at 300 messages to keep the database document small; raise it if you want more history kept.
