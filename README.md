# Protein Pal

*A tiny companion with a big mission: help you hit your protein goal — every single day.*

<p align="center">
  <img src="https://img.shields.io/badge/stack-HTML%20%7C%20CSS%20%7C%20JS-blue" />
  <img src="https://img.shields.io/badge/backend-Firebase-orange" />
  <img src="https://img.shields.io/badge/ui-dark%20mode-111" />
</p>

---

## The Story

You set a goal. Life gets busy. Protein Pal steps in.

This project began with a simple question: *“What if tracking protein felt as light as sending a text?”* No spreadsheets. No multi‑step forms. Just a clean daily view, a playful progress ring, and a weekly pulse check to keep the streak alive.

Protein Pal is a single‑page web app that lets you:

* register your own account,
* log meals for **today or any past day**, and
* instantly see how close you are to your goal — with a nudge of motivation when you need it.

It’s deliberately minimal, fast, and privacy‑respecting. Built with **vanilla JavaScript** on the front, and **Firebase Authentication + Firestore** on the back for real‑time magic.

> **Project status**: Originally built in **2020** during a personal fat‑loss journey and refreshed in **2025** for open‑sourcing. Maintained **as‑is** with minor polish.

---

## Backstory & Motivation

In 2020, while focusing on fat loss, I needed a frictionless way to hit a daily protein target. Most apps felt bloated, so I built Protein Pal to be the opposite: fast, single‑purpose, and encouraging. The app’s progress ring and weekly chart became my “daily nudge,” and the flexible date logging helped me stay consistent even when I logged meals later.

In 2025 I decided to share it on GitHub. Even though the stack is intentionally simple, the project showcases pragmatic design, secure Firebase rules, and a clean UX that still holds up.

---

## Table of Contents

1. [Why Protein Pal?](#why-protein-pal)
2. [Features at a Glance](#features-at-a-glance)
3. [How It Feels](#how-it-feels)
4. [Architecture](#architecture)
5. [Tech Stack](#tech-stack)
6. [Getting Started](#getting-started)

   * [Create a Firebase Project](#create-a-firebase-project)
   * [Enable Authentication](#enable-authentication)
   * [Create a Firestore Database](#create-a-firestore-database)
   * [Add Your Web App Config](#add-your-web-app-config)
   * [Set Firestore Security Rules](#set-firestore-security-rules)
7. [Configure & Run](#configure--run)
8. [Using the App](#using-the-app)
9. [Data Model](#data-model)
10. [Accessibility & Responsiveness](#accessibility--responsiveness)
11. [Troubleshooting](#troubleshooting)
12. [Roadmap](#roadmap)
13. [Contributing](#contributing)
14. [License](#license)

---

## Why Protein Pal?

Nutrition apps can feel heavy. Protein Pal is intentionally light:

* **Focus**: Track one thing well — protein.
* **Flow**: Daily ring + weekly chart gives a calm, at‑a‑glance rhythm.
* **Freedom**: Log for any date. Fix yesterday. Plan tomorrow.
* **Privacy**: Your data is yours; secured by Firebase auth & rules.
* **Delight**: Dark mode, crisp typography, and encouraging quotes when you’re just shy of the goal.

---

## Features at a Glance

* **Secure User Authentication** — Email/password registration & login. Use your account anywhere.
* **Dynamic Daily Dashboard** — A progress ring shows how much protein you’ve clocked vs. your goal. Title & totals follow the date you pick.
* **Custom Meal Logging** — Add food name, quantity, protein grams, and meal type (Breakfast/Lunch/Dinner/etc.).
* **Flexible Date Entry** — Log past days or adjust old entries; the whole dashboard syncs to the selected date.
* **Weekly Progress Chart** — A responsive Chart.js bar chart for the week, with a goal line for easy comparison.
* **Motivational Quotes** — Friendly nudges if you haven’t met your goal yet.
* **Theme Toggle** — Light ↔ Dark; your preference is remembered locally.
* **Real‑Time Database** — Firestore keeps everything instantly in sync.
* **Fully Responsive** — Designed for desktop, tablet, and mobile.

---

## How It Feels

1. **Sign in**. Meet a clean dashboard with today pre‑selected.
2. **Set your goal** (e.g., 110g). Watch the ring animate as you log meals.
3. **Pick a date** to revisit last Thursday’s dinner or plan ahead.
4. **Glance at your week**. The bars tell the story; the line keeps you honest.
5. **Smile at the quote**. Then add that last 20g and close the loop.

---

## Architecture

```
client (Vanilla JS)
  ├─ UI (HTML/CSS + Tailwind)
  ├─ State (selectedDate, user, goal, dailyTotals)
  ├─ Services
  │    ├─ auth.js      (Firebase Auth)
  │    ├─ db.js        (Firestore CRUD)
  │    └─ charts.js    (Chart.js rendering)
  └─ Components
       ├─ ProgressRing
       ├─ MealForm
       ├─ WeeklyChart
       └─ ThemeToggle

backend (Firebase)
  ├─ Authentication (Email/Password)
  └─ Firestore
       ├─ /usernames/{username}
       └─ /users/{uid}/...
```

**Security**: Firestore rules ensure each user can only read/write their own data and that a username creation ties to the authenticated UID.

---

## Tech Stack

**Frontend**: HTML5, CSS3, **Vanilla JavaScript (ES6 Modules)**
**Styling**: **Tailwind CSS**
**Charts**: **Chart.js**
**Backend & Database**: **Google Firebase** (Auth + Firestore)

---

## Getting Started

### Create a Firebase Project

1. Open the **Firebase Console** and create a new project (e.g., `my-protein-tracker`).

### Enable Authentication

1. Go to **Build → Authentication**.
2. Open **Sign‑in method**.
3. **Enable Email/Password**.
4. Under **Settings → Authorized domains**, add `localhost` and (if you use a preview environment) `scf.usercontent.goog`.

### Create a Firestore Database

1. Go to **Build → Firestore Database**.
2. Click **Create database** → start in **Production mode**.
3. Choose a region close to your users.

### Add Your Web App Config

1. Go to **Project Settings (gear icon)**.
2. Under **Your apps**, click the **Web** icon (</>) to register a web app.
3. Give it a nickname and click **Register app** (Hosting not required).
4. Copy the generated `firebaseConfig` object.

### Set Firestore Security Rules

Replace default rules with the following and **Publish**:

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /usernames/{username} {
      allow read;
      allow create: if request.auth != null && request.resource.data.uid == request.auth.uid;
    }
    match /users/{userId}/{allPaths=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

---

## Configure & Run

1. **Clone** the repository and open it in your editor.
2. Open `index.html` and find:

   ```html
   <script>
     const firebaseConfig = { /* ... */ };
     // initializeApp(firebaseConfig)
   </script>
   ```
3. **Paste** your `firebaseConfig` from Firebase into that object.
4. Start a local server (any static server works):

   * VS Code Live Server, or
   * `python3 -m http.server 5173` and open `http://localhost:5173`.

> No build step required — it’s pure HTML/CSS/JS.

---

## Using the App

* **Register / Sign in** with email & password.
* **Set or edit your daily goal** from the dashboard.
* **Log a meal** with:

  * Food name
  * Quantity/portion
  * Protein grams (g)
  * Meal type (Breakfast, Lunch, Dinner, Snack)
* **Change date** from the date picker to log past days or review entries.
* **Check the weekly chart** to compare each day against your goal line.
* **Toggle theme** from the header; your choice is saved locally.

---

## Data Model

```
/usernames/{username}
  uid: string

/users/{uid}
  profile
    goal: number          // grams per day
    createdAt: timestamp
  meals (collection)
    {yyyy-mm-dd}
      entries: [
        {
          id: string,
          food: string,
          quantity: string,
          protein: number,
          type: 'Breakfast'|'Lunch'|'Dinner'|'Snack',
          createdAt: timestamp
        }, ...
      ]
      totalProtein: number
```

> The UI derives **daily totals** and **weekly aggregates** from the `meals` collection in real time.

---

## Accessibility & Responsiveness

* Logical heading order and labeled inputs for assistive tech.
* Sufficient contrast in both light and dark themes.
* Keyboard‑navigable form controls.
* Responsive layout scales from mobile to desktop.

---

## Troubleshooting

* **Auth/permission errors**: Re‑check Firestore Rules and ensure you’re authenticated.
* **Nothing loads**: Verify your `firebaseConfig` and that the Firestore region is available.
* **CORS / domain issues**: Add your local or preview domain under **Authorized domains** in Authentication settings.
* **Charts not updating**: Confirm date state is changing and meals are written under the selected `yyyy-mm-dd`.

---

## Roadmap

* Editable past entries (inline)
* Goal suggestions (based on body weight preference)
* Export to CSV
* Streaks & badges
* Offline‑first caching

---

## Contributing

PRs welcome! Please open an issue to discuss significant changes. Keep the scope small and the UI simple.

---

## License

MIT — do great things. 
