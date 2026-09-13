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

## One remaining setup step: enable Email/Password sign-in
The app now has username/password accounts (a username is turned into an
internal `username@kotoba-vocab.app` email behind the scenes — Firebase
Auth needs an email format, but users never see or type an email).
In Firebase Console → Build → Authentication → Sign-in method, enable the
**Email/Password** provider. Without this, sign-up and login will fail.

## Firestore security rules
Now that there are real accounts, each person should only be able to
read/write their own progress document. In Firebase Console → Firestore
Database → Rules, use:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /vocabProgress/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Click **Publish**. This replaces the earlier open `allow read, write: if true`
rule — it's stricter and doesn't expire, since it checks that whoever is
reading/writing a progress document is logged in as that same user.

## Note on existing progress
Progress that was saved before under a random per-browser device ID is
not automatically linked to a new account — signing up creates a fresh,
empty progress record tied to the account instead.
