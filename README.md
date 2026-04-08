Personal Notes App (Assignment 3)
Flutter + Firebase app: email/password auth, private Firestore notes with realtime updates, route guards, and secure JWT storage.

Stack
Flutter 3.10+
go_router, Riverpod
Firebase Auth, Cloud Firestore
flutter_secure_storage, jwt_decoder
Architecture (feature-first)
lib/
  app/              # MaterialApp + go_router
  core/             # firebase init, providers, token/secure storage, errors
  auth/             # domain/repo/controllers + screens (splash/login/register/profile)
  notes/            # domain/repo/controllers + screens (list/editor)
Repository interfaces in domain, implementations in data.
DI via Riverpod providers.
go_router guard protects all routes after /login and /register.
Routes
/ Splash (checks remember-me)
/login, /register
/home notes list
/note/:id editor (id = new → create)
/profile logout
Auth & Token
Firebase Email/Password.
ID token (JWT) stored in flutter_secure_storage.
TokenManager.ensureValidToken calls user.getIdToken(true) when expired.
“Remember me”: if off, cold start forces logout.
Logout clears secure token and Firebase session.
Firestore
Collection: users/{uid}/notes/{noteId}
Fields: title, body, createdAt, updatedAt
Streams via StreamProvider → live list updates.
Offline cache enabled in initializeFirebase().
Suggested security rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/notes/{noteId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
Firebase setup (required)
Install CLI: dart pub global activate flutterfire_cli
In project root run: flutterfire configure (select your Firebase project; enable Android/iOS/Web).
This regenerates lib/firebase_options.dart with real keys.
Add platform config files (do NOT commit):
android/app/google-services.json
ios/Runner/GoogleService-Info.plist
Restart flutter run -d chrome or your target.
Without these files the app will show “Setup required”.

Build & run
Get deps: flutter pub get
Web dev: flutter run -d chrome
APK: flutter build apk
Tests: flutter test (currently minimal)
Submission checklist
2‑min screen recording: register → login → create/update/delete note → logout
Repo link md2-assignment-3
Provide google-services.json / GoogleService-Info.plist separately (not in git)
Provide debug APK link
