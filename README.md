# WhatsApp Clone

A modern Android WhatsApp clone built with Kotlin, Jetpack Compose, Firebase, and Material 3. The project recreates the core messaging experience of WhatsApp, including phone/Google authentication, real-time chat lists, one-to-one conversations, media messages, profile management, online presence, theme switching, and WhatsApp-inspired light/dark UI styling.

> **Disclaimer:** This is an educational clone project and is not affiliated with, endorsed by, or connected to WhatsApp LLC or Meta Platforms, Inc.

## Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Important App Flows](#important-app-flows)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Firebase Data Model](#firebase-data-model)
- [API Reference / Documentation](#api-reference--documentation)
- [Project Structure](#project-structure)
- [Screenshots](#screenshots)
- [Contributing](#contributing)
- [Roadmap](#roadmap)
- [License](#license)

## Overview

This project demonstrates a real-time chat application inspired by WhatsApp. It uses Firebase as the backend layer for authentication, realtime message synchronization, media storage, and user presence. The Android client is built fully with Jetpack Compose and follows a clean MVVM-style structure with repository abstractions and dependency injection.

The goal is to provide a practical reference for building a production-style messaging UI with modern Android tools while keeping the codebase readable and extensible.

## Tech Stack

| Area | Technology |
| --- | --- |
| Language | Kotlin |
| UI | Jetpack Compose, Material 3 |
| Architecture | MVVM, Repository Pattern |
| Navigation | AndroidX Navigation 3 |
| Dependency Injection | Koin |
| Backend | Firebase Authentication, Firebase Realtime Database, Firebase Storage, Firebase Cloud Messaging |
| Media | CameraX, Android media picker |
| Images | Coil |
| Local Preferences | Jetpack DataStore Preferences |
| Background Work | WorkManager |
| Build System | Gradle Kotlin DSL |

## Architecture

The app is organized around a Compose-first MVVM architecture:

```text
UI Screens
   |
   v
ViewModels
   |
   v
Repository Interfaces
   |
   v
Firebase Repository Implementations
   |
   v
Firebase Auth / Realtime Database / Storage / Messaging
```

### Main Layers

- **Presentation layer:** Compose screens and ViewModels under `presentation/`.
- **Navigation layer:** Navigation 3 routes and transitions under `navigation/`.
- **Domain/data contracts:** Repository interfaces and data classes under `model/`.
- **Data layer:** Firebase-backed repository implementations under `model/repository/`.
- **Dependency injection:** Koin modules under `di/`.
- **Theme layer:** WhatsApp-style Material theme tokens under `ui/theme/`.

### Dependency Injection

Koin wires Firebase services, repositories, ViewModels, presence tracking, and theme preference storage from `Appmodules.kt`.

## Important App Flows

### Authentication Flow

```text
PhoneEntryScreen
   |-- Phone OTP login
   |      |
   |      v
   |   OtpVerifyScreen
   |      |
   |      v
   |   ProfileSetupScreen or HomeScreen
   |
   `-- Google Sign-In
          |
          v
       HomeScreen
```

- Phone authentication uses Firebase Phone Auth.
- Google sign-in uses Android Credential Manager and Firebase Auth.
- Existing users skip profile setup when a saved profile is found.

### Chat Flow

```text
HomeScreen
   |
   v
NewChatScreen
   |
   v
createOrGetChat(peerId)
   |
   v
ConversationScreen
   |
   v
sendTextMessage() / sendMediaMessage()
```

- Chat lists are observed from Firebase Realtime Database.
- Conversations stream message updates in real time.
- Chat previews and unread counts are updated after every sent message.

### Presence Flow

```text
MainActivity
   |
   v
PresenceRepository.initPresence()
   |
   v
Firebase .info/connected
   |
   v
isOnline + lastSeen updates
```

- Online state is written when the user connects.
- `onDisconnect()` marks users offline and stores `lastSeen`.

### Theme Flow

```text
SettingsScreen
   |
   v
ThemePreferenceRepository
   |
   v
DataStore
   |
   v
WhatsAppTheme
```

- Supports light, dark, and system default themes.
- App-specific colors are provided through `LocalAppColors`.

## Features

- Phone number authentication with OTP.
- Google sign-in support.
- User profile setup and editing.
- Real-time chat list updates.
- One-to-one conversations.
- Text, image, video, audio, and document message support.
- Firebase Storage media uploads.
- Online/offline presence and last seen display.
- Search users by name or phone number.
- Swipe-to-delete chat rows.
- WhatsApp-inspired light and dark themes.
- Smooth screen transition animations.
- Camera capture flow using CameraX.
- Push notification infrastructure with Firebase Messaging.
- Theme persistence using DataStore.

## Prerequisites

Before running the project, install or configure:

- Android Studio latest stable version.
- JDK 11 or newer.
- Android SDK with API 37 installed.
- A Firebase project.
- An Android emulator or physical Android device.
- Google services configuration file: `app/google-services.json`.
- A valid web client ID for Google Sign-In.

## Installation

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd WhatsApp
```

### 2. Add Firebase configuration

Download `google-services.json` from your Firebase console and place it here:

```text
app/google-services.json
```

### 3. Configure local properties

Create or update `local.properties` in the project root:

```properties
sdk.dir=/path/to/your/android/sdk
WEB_CLIENT_ID=your_google_web_client_id_here
```

On Windows, the SDK path usually looks like:

```properties
sdk.dir=C:\\Users\\<your-user>\\AppData\\Local\\Android\\Sdk
WEB_CLIENT_ID=your_google_web_client_id_here
```

### 4. Enable Firebase services

In Firebase Console, enable:

- Authentication
  - Phone provider
  - Google provider
- Realtime Database
- Firebase Storage
- Firebase Cloud Messaging

### 5. Build the project

```bash
./gradlew :app:assembleDebug
```

On Windows PowerShell:

```powershell
.\gradlew.bat :app:assembleDebug
```

### 6. Run the app

Open the project in Android Studio and run the `app` configuration, or install the debug APK manually:

```bash
adb install app/build/outputs/apk/debug/app-debug.apk
```

## Usage

### Run Kotlin compilation check

```bash
./gradlew :app:compileDebugKotlin
```

### Build debug APK

```bash
./gradlew :app:assembleDebug
```

### Clean build artifacts

```bash
./gradlew clean
```

### Basic user journey

1. Launch the app.
2. Sign in using phone OTP or Google Sign-In.
3. Complete profile setup if required.
4. Search for another registered user.
5. Start a chat.
6. Send text or media messages.
7. Update theme from Settings.

## Firebase Data Model

The app uses Firebase Realtime Database with a structure similar to:

```text
users
  {uid}
    uid
    phoneNumber
    displayName
    avatarUrl
    about
    isOnline
    lastSeen

user_chats
  {uid}
    {chatId}
      id
      peerId
      peerName
      peerAvatar
      lastMessage
      lastMessageTime
      unreadCount

messages
  {chatId}
    {messageId}
      id
      chatId
      senderId
      text
      mediaUrl
      mediaType
      fileName
      status
      timestamp
      readBy
```

Firebase Storage paths used by the app:

```text
avatars/{uid}.jpg
chats/{chatId}/{messageId}
```

## API Reference / Documentation

This project does not expose a custom REST API. It communicates directly with Firebase SDKs from the Android client.

Useful external documentation:

- [Firebase Authentication](https://firebase.google.com/docs/auth)
- [Firebase Realtime Database](https://firebase.google.com/docs/database)
- [Firebase Storage](https://firebase.google.com/docs/storage)
- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging)
- [Jetpack Compose](https://developer.android.com/compose)
- [CameraX](https://developer.android.com/media/camera/camerax)
- [Koin](https://insert-koin.io/)

## Project Structure

```text
app/src/main/java/com/android/whatsapp
|-- MainActivity.kt
|-- WhatsAppApplication.kt
|-- di/
|   `-- Appmodules.kt
|-- fmc/
|   `-- WhatsAppMessagingService.kt
|-- model/
|   |-- dataclass/
|   `-- repository/
|-- navigation/
|   |-- AppNavGraph.kt
|   `-- Routes.kt
|-- notification/
|   `-- MessageNotificationWorker.kt
|-- presentation/
|   |-- auth/
|   |-- camera/
|   |-- calls/
|   |-- chat/
|   |-- home/
|   |-- newchat/
|   |-- profile/
|   |-- settings/
|   `-- status/
`-- ui/
    `-- theme/
```

## Screenshots

Add screenshots manually after capturing them from the emulator or a real device.

Suggested layout:

| Light Home | Dark Home | Conversation |
| --- | --- | --- |
| `screenshots/light-home.png` | `screenshots/dark-home.png` | `screenshots/conversation.png` |

| Authentication | Settings | Profile |
| --- | --- | --- |
| `screenshots/auth.png` | `screenshots/settings.png` | `screenshots/profile.png` |

Example Markdown:

```md
![Light Home](screenshots/light-home.png)
![Conversation](screenshots/conversation.png)
```

## Contributing

Contributions are welcome. To contribute:

1. Fork the repository.
2. Create a feature branch.

```bash
git checkout -b feature/your-feature-name
```

3. Make focused changes.
4. Run a compile/build check.

```bash
./gradlew :app:compileDebugKotlin
```

5. Commit your changes.

```bash
git commit -m "Add your feature"
```

6. Push your branch and open a pull request.

```bash
git push origin feature/your-feature-name
```

### Contribution Guidelines

- Keep UI changes consistent with the WhatsApp-inspired Material theme.
- Avoid committing secrets, API keys, or personal Firebase files.
- Keep pull requests focused and easy to review.
- Include screenshots for UI changes when possible.
- Explain any Firebase schema or security rule changes clearly.

## Roadmap

- Group chats.
- Status/updates implementation.
- Voice and video call integration.
- Message reactions.
- Message forwarding and replies.
- End-to-end encryption learning prototype.
- Better notification grouping.
- Firebase security rules documentation.

## License

No license has been specified yet. Add a license before publishing or accepting external contributions.
