# Zielki Timer

A free, web-based countdown timer for tracking kids' screen time on devices. Multiple family members open the same URL on different phones/tablets/laptops and see the same timers in real-time.

**Live example URL:** `https://cybul3000.github.io/zielki_timer/#smith-family`

---

## Features

- Named countdown timers (e.g. "iPad", "TV Time", "Gaming")
- Real-time sync across all devices (sub-second latency)
- Circular gauge that turns green → yellow → red as time runs low
- Room-based access — share a URL and the whole family is synced
- Works offline; resumes from correct time when reconnected
- Free forever (Firebase Spark plan + GitHub Pages)

---

## Setup

### Step 1 — Create a Firebase Project

1. Go to [https://console.firebase.google.com](https://console.firebase.google.com) and sign in with a Google account.
2. Click **Add project**, give it a name (e.g. `zielki-timer`), click through the prompts.
3. Once created, click **Realtime Database** in the left menu → **Create database**.
4. Choose a location (any) → Select **Start in test mode** → **Enable**.

### Step 2 — Get the Firebase Config

1. In the Firebase console, click the gear icon ⚙ → **Project settings**.
2. Scroll to **Your apps** → click **Add app** → choose the Web icon `</>`.
3. Register the app (nickname anything, no Hosting needed here).
4. Copy the `firebaseConfig` object — it looks like this:

```js
const firebaseConfig = {
  apiKey:            "AIzaSy...",
  authDomain:        "your-project.firebaseapp.com",
  databaseURL:       "https://your-project-default-rtdb.firebaseio.com",
  projectId:         "your-project",
  storageBucket:     "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123456789:web:abc123"
};
```

### Step 3 — Paste the Config into index.html

Open `index.html` and find this block near the top of the `<script>` section:

```js
const FIREBASE_CONFIG = {
  apiKey:            "YOUR_API_KEY",
  authDomain:        "YOUR_PROJECT_ID.firebaseapp.com",
  databaseURL:       "https://YOUR_PROJECT_ID-default-rtdb.firebaseio.com",
  ...
};
```

Replace every `YOUR_*` placeholder with the actual values from Step 2.

### Step 4 — Set Firebase Security Rules

In the Firebase console → **Realtime Database** → **Rules** tab, paste:

```json
{
  "rules": {
    "rooms": {
      "$roomId": {
        ".read":  true,
        ".write": true
      }
    }
  }
}
```

Click **Publish**. This allows anyone with the room URL to read and write timers — security is through the obscurity of your room code.

### Step 5 — Deploy to GitHub Pages

1. Push your code to the `main` branch of your GitHub repository.
2. Go to the repo on GitHub → **Settings** → **Pages**.
3. Under **Branch**, select `main` and folder `/ (root)` → click **Save**.
4. After a minute, your app is live at:
   `https://<your-username>.github.io/<your-repo-name>/`

---

## Usage

1. Open the app URL in your browser.
2. Type a room code (e.g. `smith-family`) and press **Enter Room**.
3. The URL becomes `https://…/#smith-family` — **share this URL** with family members.
4. Click **+ Add Timer**, enter a name and duration, click **Create**.
5. Press ▶ to start the countdown. Anyone watching the same URL sees it tick down live.

### Room Code Tips

- Use something memorable but not easily guessable: `jones-home`, `peter-kids`, `screen-time-2024`
- Letters, numbers, hyphens (`-`) and underscores (`_`) only
- Different families using the same hosted app just need different room codes

---

## Alternative: Firebase Hosting (instead of GitHub Pages)

If you prefer Firebase Hosting (custom domain support, instant cache invalidation):

```bash
npm install -g firebase-tools
firebase login
firebase init hosting   # point public dir to ".", SPA rewrites yes
firebase deploy
```

Your app will be live at `https://<project-id>.web.app`.

---

## Architecture

| Component | Technology | Cost |
|---|---|---|
| Frontend | Vanilla HTML/CSS/JS, single file | Free |
| Real-time sync | Firebase Realtime Database (Spark plan) | Free |
| Hosting | GitHub Pages | Free |
| SDK | Firebase 9 compat via CDN | Free |

**Sync model:** When a timer is started, an `endsAt` absolute timestamp is written to Firebase. All connected devices subtract `Date.now()` from `endsAt` locally every second — no server-side countdown needed. Clock skew is corrected via Firebase's `.info/serverTimeOffset`.

---

## Firebase Free Tier Limits (Spark Plan)

| Resource | Free Limit |
|---|---|
| Simultaneous connections | 100 |
| Storage | 1 GB |
| Download / month | 10 GB |

More than sufficient for family use.
