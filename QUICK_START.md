# Quick Start - Convay Meet Android SDK (Public Maven Repo)

Use this guide to add the SDK and launch meetings in any Android app.

## ✅ 1) Add Maven Repo (Public)

If you are using Gradle 7+ with `settings.gradle`, add repositories there.
Otherwise add them in the project-level `build.gradle`.

**Kotlin DSL (settings.gradle.kts)**

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven {
            url = uri("https://raw.githubusercontent.com/Synesis-IT-PLC/convay-maven-repository/master/releases")
        }
    }
}
```

**Groovy (build.gradle - Project Level)**

```gradle
allprojects {
    repositories {
        google()
        mavenCentral()
        maven { url = uri("https://raw.githubusercontent.com/Synesis-IT-PLC/convay-maven-repository/master/releases") }
    }
}
```

## ✅ 2) Add SDK Dependency

In app module `build.gradle`:

```gradle
dependencies {
    implementation "org.convay.react:convay-meet-sdk:1.0.1"
}
```

## ✅ 3) Minimum SDK

Ensure:

```gradle
minSdkVersion 24
```

## ✅ 4) Permissions

In `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
```

If you need screenshare:

```xml
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_MEDIA_PROJECTION" />
```

## ✅ 5) Enable MediaProjection (optional screenshare)

In `MainApplication.java`:

```java
import com.oney.WebRTCModule.WebRTCModuleOptions;

WebRTCModuleOptions options = WebRTCModuleOptions.getInstance();
options.enableMediaProjectionService = true;
```

## ✅ 6) Sync + Build

Run:

```bash
./gradlew clean build
```

---

# Use the SDK

## Start meeting (token-based)

```java
ConvayMeetConferenceOptions options =
    new ConvayMeetConferenceOptions.Builder()
        .setAuthToken(authToken) // required
        .setFeatureFlag("startpage.enabled", true) // required
        .setUserInfo(userInfo) // optional
        .setAudioMuted(false) // optional
        .setVideoMuted(false) // optional
        .setFeatureFlag("chat.enabled", true)       // optional
        .setFeatureFlag("invite.enabled", true)     // optional
        .setFeatureFlag("recording.enabled", false) // optional
        .setFeatureFlag("pip.enabled", true)        // optional
        .build();

ConvayMeetActivity.launch(this, options);
```

## Join meeting (link-based)

Sample meeting link:
`https://convay.com/m/j/649779334794/maksudurrahman?pwd=fb2f30e4532f6ed6c8486cc494560904`

```java
ConvayMeetConferenceOptions options =
    new ConvayMeetConferenceOptions.Builder()
        .setMeetingLink(meetingLink) // required
        .setFeatureFlag("joinpage.enabled", true) // required
        .setUserInfo(userInfo) // optional
        .setAudioMuted(false) // optional
        .setVideoMuted(false) // optional
        .setFeatureFlag("chat.enabled", true)       // optional
        .setFeatureFlag("invite.enabled", true)     // optional
        .setFeatureFlag("recording.enabled", false) // optional
        .setFeatureFlag("pip.enabled", true)        // optional
        .build();

ConvayMeetActivity.launch(this, options);
```

## Alternatively, use ConvayMeetView

```java
ConvayMeetView convayMeetView = new ConvayMeetView(this);

ConvayMeetConferenceOptions options =
    new ConvayMeetConferenceOptions.Builder()
        .setToken(authToken) // required for start meeting
        .setFeatureFlag("startpage.enabled", true) // required
        .setUserInfo(userInfo) // optional
        .setAudioMuted(false) // optional
        .setVideoMuted(false) // optional
        .setFeatureFlag("chat.enabled", true)       // optional
        .setFeatureFlag("invite.enabled", true)     // optional
        .setFeatureFlag("recording.enabled", false) // optional
        .setFeatureFlag("pip.enabled", true)        // optional
        .build();

convayMeetView.join(options);
setContentView(convayMeetView);
```

## Optional defaults

```java
ConvayMeetConferenceOptions defaultOptions =
    new ConvayMeetConferenceOptions.Builder()
        .setUserInfo(defaultUserInfo)
        .setAudioMuted(false)
        .setVideoMuted(false)
        .build();

ConvayMeet.setDefaultConferenceOptions(defaultOptions);
```

## Variable Details

- `authToken`: Enterprise authentication token used for start meeting flow.
- `meetingLink`: Full join URL for the meeting (include protocol and path).
- `userInfo`: Optional user profile (name, email, avatar) shown in the meeting UI.
- `audioMuted` / `videoMuted`: Initial media state when the meeting starts.


## Feature Flags

- `startpage.enabled`: Required for start meeting flow (token-based)
- `joinpage.enabled`: Required for join meeting flow (link-based)
- `chat.enabled`: Enable in-meeting chat
- `invite.enabled`: Enable invite UI
- `recording.enabled`: Enable or disable recording controls
- `pip.enabled`: Enable picture-in-picture on Android

---

## 🧪 Sample Android App

Reference implementation:
https://github.com/Synesis-IT-PLC/convay-meet-sdk-samples/tree/main/android