# Kotoba — Japanese Vocabulary App

A single-page Japanese vocabulary trainer: words organized by category and
subcategory (numbers/counters, food, family mealtime small-talk & gossip,
etc.), flip-to-reveal cards, a "don't know yet" stack, and a one-word-at-a-
time game mode where missed words are requeued and moved to the stack after
a third miss.

## Stack
- Static HTML/CSS/JS, no build step
- Firebase Firestore for persisting progress (per-browser device ID, no login)
- Deployed via Vercel (auto-deploys on every push to `main`)

## One remaining setup step: Firestore security rules
The project was created in Firestore **test mode**, which stops allowing
writes automatically after ~30 days. Since this app stores no sensitive
data (just vocab flashcard progress), the simplest fix is an open rule.
In Firebase Console → Firestore Database → Rules, replace the contents with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

Click **Publish**. Without this, saving progress will silently stop working
once the test-mode window expires.
