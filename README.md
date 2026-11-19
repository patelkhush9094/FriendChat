# FriendChat — Private Real-Time Android Messaging Application

**FriendChat** is a lightweight, private, real-time native Android text messaging application built exclusively for close friends.

---

## 📱 Key Highlights & Architecture

- **Platform:** Native Android (Min SDK 24 / Target SDK 35)
- **Language:** **100% Java** (No Kotlin, Flutter, or React Native)
- **UI & Layouts:** XML Layouts styled with Material Design 3 and a **Dark Navy & Midnight Black theme with electric blue accents**
- **Authentication:** Firebase Authentication (Email & Password with validation, unique usernames, and password reset)
- **Real-Time Database:** Cloud Firestore with real-time snapshot listeners for instant message delivery
- **Offline Persistence:** Firestore offline cache enabled
- **Security:** Strict, production-ready Firestore Security Rules (`firestore.rules`)
- **Deterministic Conversations:** Lexicographically ordered conversation IDs (`uidA_uidB`) preventing duplicate threads
- **Push Notifications:** Firebase Cloud Messaging (FCM) service ready

---

## 📂 Project Structure

```
chatapp/
├── app/
│   ├── build.gradle                              # App dependencies & SDK configuration
│   ├── google-services.json                      # Firebase configuration file placeholder
│   ├── proguard-rules.pro                        # ProGuard obfuscation & keeping rules
│   └── src/
│       └── main/
│           ├── AndroidManifest.xml               # App manifest, permissions & activities
│           ├── java/com/friendchat/app/
│           │   ├── FriendChatApp.java            # Application class (offline cache & notifications)
│           │   ├── activities/
│           │   │   ├── SplashActivity.java       # Checks auth state & routes user
│           │   │   ├── LoginActivity.java        # Email/password login & password reset
│           │   │   ├── RegisterActivity.java     # Registration with unique username verification
│           │   │   ├── MainActivity.java         # Hosts 4 bottom navigation tabs
│           │   │   ├── SearchUsersActivity.java  # Search friends by username
│           │   │   ├── ChatActivity.java         # Real-time private chat & messaging
│           │   │   └── EditProfileActivity.java  # Edit display name & profile avatar
│           │   ├── fragments/
│           │   │   ├── ChatsFragment.java        # Conversation list with last message preview
│           │   │   ├── FriendsFragment.java      # Friends list with search & remove options
│           │   │   ├── RequestsFragment.java     # Received & Sent friend requests
│           │   │   └── ProfileFragment.java      # User profile details & logout
│           │   ├── adapters/
│           │   │   ├── ConversationAdapter.java  # Recycler adapter for active chats
│           │   │   ├── MessageAdapter.java       # Recycler adapter with sent/received bubbles
│           │   │   ├── FriendAdapter.java        # Recycler adapter for friends
│           │   │   ├── FriendRequestAdapter.java # Recycler adapter for friend requests
│           │   │   └── UserSearchAdapter.java    # Recycler adapter for searching users
│           │   ├── models/
│           │   │   ├── User.java                 # User profile data model
│           │   │   ├── FriendRequest.java        # Friend request model (PENDING/ACCEPTED/REJECTED)
│           │   │   ├── Friend.java               # Friend model
│           │   │   ├── Conversation.java         # Conversation metadata model
│           │   │   └── Message.java              # Chat message model
│           │   ├── repositories/
│           │   │   ├── AuthRepository.java       # Firebase Authentication operations
│           │   │   ├── UserRepository.java       # Firestore user profile management & search
│           │   │   ├── FriendRepository.java     # Friend requests & atomic batch acceptance
│           │   │   └── ChatRepository.java       # Real-time messaging & conversation logic
│           │   ├── services/
│           │   │   └── FriendChatFCMService.java # Firebase Cloud Messaging notifications
│           │   └── utils/
│           │       ├── Constants.java            # App-wide constants & Firestore collection names
│           │       ├── ConversationUtils.java    # Deterministic conversation ID generator
│           │       ├── DateTimeUtils.java        # Human-readable date & time formatters
│           │       └── NetworkUtils.java         # Connectivity helper
│           └── res/
│               ├── color/                        # Navigation selector colors
│               ├── drawable/                     # Message bubbles, icons, avatars & shapes
│               ├── layout/                       # Clean XML layouts for all screens
│               ├── menu/                         # Bottom navigation menu
│               └── values/                       # colors.xml, strings.xml, themes.xml
├── gradle/wrapper/                               # Gradle wrapper properties & jar
├── firestore.rules                               # Production Firestore Security Rules
├── build.gradle                                  # Top-level build configuration
├── settings.gradle                               # Module & repository settings
└── README.md
```

---

## 🛠️ Part A: Things Already Implemented

1. **Complete Java Architecture:** Clean separation of concerns across `activities`, `fragments`, `adapters`, `models`, `repositories`, `services`, and `utils`.
2. **Registration Flow:**
   - Validates display name, username format (alphanumeric 3-20 chars), email format, and password matching (minimum 6 chars).
   - Asynchronously queries Firestore to verify username uniqueness before creating the account.
   - Stores user document in `users/{userId}`.
3. **Login & Session Management:**
   - Firebase Auth login with email & password.
   - Password reset dialog that triggers reset emails.
   - Persistent login: users stay logged in when closing and reopening the app.
4. **Bottom Navigation (4 Tabs):**
   - **Chats Tab:** Active conversations, last message preview, last timestamp, unread indicator.
   - **Friends Tab:** Accepted friends list, quick "Message" button, options popup to remove friend.
   - **Requests Tab:** Toggle between "Received" requests (with Accept & Reject buttons) and "Sent" requests (with Pending badge & Cancel button). Real-time badge counter on bottom navigation.
   - **Profile Tab:** Profile picture, display name, `@username`, email, account creation date, edit profile button, and logout with confirmation.
5. **Friend Search & Request Engine:**
   - Search by username prefix.
   - Prevents sending requests to oneself.
   - Detects if users are already friends or have requests pending.
   - Atomic acceptance via Firestore `WriteBatch`: sets request to `ACCEPTED` and writes friend documents to both users' `friends/{uid}/friendList/{friendId}` simultaneously.
6. **Real-Time Private Chat:**
   - Deterministic conversation ID: `ConversationUtils.getConversationId(uid1, uid2)` prevents duplicate conversations.
   - Real-time snapshot listener on subcollection `conversations/{id}/messages` ordered chronologically.
   - Rounded chat bubbles: royal blue for sent messages, slate navy for received messages.
   - Failed message detection with tap-to-retry.
   - Empty conversation illustration state.
   - Auto-scroll to latest message on keyboard display or new message arrival.
7. **Firestore Offline Persistence:** Enabled in `FriendChatApp.java`.

---

## 🔐 Part B: What Requires Your Firebase Account

To run FriendChat with real data, Firebase requires your own Firebase project credentials:
1. Your Firebase project's `google-services.json` file.
2. Email/Password Authentication provider enabled in your Firebase Console.
3. Cloud Firestore database enabled in your Firebase Console.
4. Security rules deployed from the included `firestore.rules`.

---

## 🚀 Beginner-Friendly Firebase Setup Guide

Follow these steps to connect your Firebase account.

### STEP 1: Open the Firebase Console
1. Open your web browser and go to: [https://console.firebase.google.com/](https://console.firebase.google.com/)
2. Sign in with your Google account.

---

### STEP 2: Create a New Firebase Project
1. In the Firebase Console, click **"Add project"** (or **"Create a project"**).
2. Enter a project name, for example: `FriendChat`.
3. (Optional) Disable Google Analytics for simplicity, or leave it enabled.
4. Click **"Create project"** and wait a few seconds until the project is ready.
5. Click **"Continue"** to enter your new project dashboard.

---

### STEP 3: Add Your Android Application to Firebase
1. On your Firebase project overview page, click the **Android icon** (`</>`) to add an Android app.
2. You will see a form titled **"Register app"**.
3. In the **"Android package name"** field, enter:
   ```
   com.friendchat.app
   ```
4. In the **"App nickname"** field (optional), enter: `FriendChat`.
5. Click **"Register app"**.

---

### STEP 4: Verify Your Package Name
Your package name is set to:
```
com.friendchat.app
```
You can verify this in:
- `app/build.gradle` (under `namespace` and `applicationId`)
- `app/src/main/AndroidManifest.xml` (under `package`)

*Important:* The package name in the Firebase Console **must exactly match** `com.friendchat.app`.

---

### STEP 5: Download google-services.json
1. After clicking "Register app", Firebase displays **"Download config file"**.
2. Click the blue button: **"Download google-services.json"**.
3. Save this file to your computer.

---

### STEP 6: Place google-services.json in the App Folder
Move the downloaded `google-services.json` file into the `app` directory of this project:
```
c:\Users\zebra\OneDrive\Desktop\chatapp\app\google-services.json
```
*(Replace the placeholder file that is currently there).*

---

### STEP 7: Enable Email/Password Authentication
1. In the Firebase Console left sidebar, click **"Build"** → **"Authentication"**.
2. Click the **"Get started"** button.
3. In the **"Sign-in method"** tab, select **"Email/Password"**.
4. Switch the first toggle **"Enable"** to ON.
5. Click **"Save"**.

---

### STEP 8: Create Cloud Firestore
1. In the Firebase Console left sidebar, click **"Build"** → **"Firestore Database"**.
2. Click the **"Create database"** button.
3. Choose a Firestore location close to you (e.g., `us-central1`, `asia-south1`, etc.).
4. Choose **"Start in production mode"** (we will apply rules in Step 9).
5. Click **"Create"**.

---

### STEP 9: Deploy Firestore Security Rules
1. In the Firestore Database section, click on the **"Rules"** tab.
2. Delete whatever default code is in the editor.
3. Open the `firestore.rules` file in this project (`c:\Users\zebra\OneDrive\Desktop\chatapp\firestore.rules`).
4. Copy the entire contents of `firestore.rules` and paste them into the Firebase Console Rules editor.
5. Click **"Publish"**.

---

### STEP 10: Open in Android Studio & Sync Gradle
1. Open **Android Studio**.
2. Select **File** → **Open...**
3. Navigate to and select the folder:
   ```
   c:\Users\zebra\OneDrive\Desktop\chatapp
   ```
4. Click **OK**.
5. Android Studio will open the project and automatically download Gradle and the required dependencies.
6. If prompted, click **"Sync Now"** in the top-right notification bar.

---

### STEP 11: Run the App on Your Phone or Emulator
1. Connect your Android phone via USB (with **USB Debugging** enabled in Developer Options) OR create an Android Virtual Device (AVD Emulator) in Android Studio.
2. Select your device from the dropdown menu in the top toolbar.
3. Click the green **Run** button (▶) or press `Shift + F10`.
4. The app will build and install onto your device.

---

### STEP 12: How to Test with Friends (2 Accounts)
To test friend requests and live chat:
1. **Account 1 (Phone A or Emulator):**
   - Tap **"Sign up"**.
   - Enter:
     - Display Name: `Alex Morgan`
     - Username: `alex`
     - Email: `alex@test.com`
     - Password: `password123`
   - Tap **"Create Account"**.

2. **Account 2 (Phone B or Second Device/Browser/Emulator):**
   - Tap **"Sign up"**.
   - Enter:
     - Display Name: `Sam Taylor`
     - Username: `sam`
     - Email: `sam@test.com`
     - Password: `password123`
   - Tap **"Create Account"**.

---

### STEP 13: Test Real-Time Chatting
1. On **Account 1 (Alex)**:
   - Go to the **Friends** tab or **Chats** tab.
   - Tap **"Find Friends"** (or the search bar).
   - Type `sam`.
   - Tap **"Add Friend"**.
2. On **Account 2 (Sam)**:
   - Notice the badge on the **Requests** tab.
   - Tap the **Requests** tab.
   - Under "Received", you will see Alex's request.
   - Tap **"Accept"**.
3. Now both users are friends:
   - Tap on **Alex** (or **Sam**) in the **Friends** tab or tap **Message**.
   - Type `"Hello! How are you?"` and tap the blue **Send** button.
   - Watch the message appear instantly on both screens via the Firestore real-time listener!
   - Close the app and re-open it: all chat history is loaded from Firestore!

---

## 📦 How to Build the APK File

In Android Studio:
1. In the top menu, click **Build** → **Build Bundle(s) / APK(s)** → **Build APK(s)**.
2. Once the build finishes, Android Studio will show a popup with a **"locate"** link.
3. Your compiled APK file will be located at:
   ```
   app/build/outputs/apk/debug/app-debug.apk
   ```
4. You can send this `.apk` file directly to your friends via Google Drive, Bluetooth, or messaging, and they can install it on any Android device!
