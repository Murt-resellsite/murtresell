FIX — Real Firebase authentication + never gets locked out again
====================================================================

WHY THIS KEPT BREAKING
Both apps had a password screen, but that password was never actually
telling Firestore who you were — it was just a local check in the
browser. Every read/write to the database happened completely
anonymously as far as Firestore was concerned. That meant your Firestore
security rules had exactly two real options: wide open to literally
anyone on the internet who found your project, or "must be signed in" —
which broke everything, because the app never signed in to begin with.
That mismatch is what caused "missing or insufficient permissions."

WHAT'S FIXED
Both apps now use real (anonymous) Firebase Authentication. The moment
you unlock with the password, the app also quietly establishes a real
signed-in session with Firestore in the background. This means your
security rules can now correctly and permanently require "must be
signed in" — actual security — instead of being wide open.

ALSO FIXED: no more getting locked out
Before, ANY Firestore hiccup (bad rules, network blip, anything) bounced
you back to the login screen, even with the right password — because the
error handling treated every failure as "you're not allowed in." Now:
the password only controls the app screen. If the cloud has a problem
after that, the app just keeps working off your local data, shows a
small status indicator (top right, e.g. "🔴 Offline — working locally,
retrying...") instead of locking you out, and quietly retries every 30
seconds in the background until it reconnects. Nothing you do gets lost
while it's retrying — it's all still saved locally the whole time.

ONE-TIME STEP YOU STILL NEED TO DO
Go to Firebase Console → Firestore Database → Rules, and paste this
(replacing whatever's there), then Publish:

rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /murtresell/{docId} {
      allow read, write: if request.auth != null;
    }
    match /murtresell/{docId}/shows/{showId} {
      allow read, write: if request.auth != null;
    }
  }
}

Also go to Firebase Console → Authentication → Sign-in method, and make
sure "Anonymous" is toggled ON. Without that one toggle, the anonymous
sign-in this fix relies on will fail.

Do this once and you shouldn't need to touch Firestore rules again —
this setup is the actual correct baseline for an app like this, not a
patch.
