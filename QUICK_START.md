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
        maven { url = uri("https://jitpack.io") }
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
        maven { url = uri("https://jitpack.io") }
    }
}
```

## ✅ 2) Add SDK Dependency

In app module `build.gradle`:

```gradle
dependencies {
    implementation "org.convay.react:convay-meet-sdk:1.0.6"
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
        .setFeatureFlag("participants.enabled", false) // optional
        .setFeatureFlag("android.screensharing.enabled", false) // optional
        .setFeatureFlag("video-mute.enabled", false) // optional
        .setFeatureFlag("audio-mute.enabled", false) // optional
        .setFeatureFlag("notifications.enabled", false) // optional
        .setFeatureFlag("filmstrip.enabled", false) // optional
        .setFeatureFlag("screenshare.landscape.enabled", true) // optional
        // Hide self-view by default
        .setConfigOverride("disableSelfView", true)
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
        .setFeatureFlag("participants.enabled", false) // optional
        .setFeatureFlag("android.screensharing.enabled", false) // optional
        .setFeatureFlag("video-mute.enabled", false) // optional
        .setFeatureFlag("audio-mute.enabled", false) // optional
        .setFeatureFlag("notifications.enabled", false) // optional
        .setFeatureFlag("filmstrip.enabled", false) // optional
        .setFeatureFlag("screenshare.landscape.enabled", true) // optional
        // Hide self-view by default
        .setConfigOverride("disableSelfView", true)
        .build();

ConvayMeetActivity.launch(this, options);
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

## Show custom UI when SDK ends or user leaves

When a meeting ends **or the user leaves**, listen for `READY_TO_CLOSE` (leave/close) or `CONFERENCE_TERMINATED` (host ended) and show your UI.

**A) ConvayMeetActivity (recommended)**

Create your own activity and override the callbacks:

```java
public class MyMeetActivity extends ConvayMeetActivity {
    @Override
    protected void onReadyToClose() {
        // Meeting ended or user left
        startActivity(new Intent(this, MyMeetingEndedActivity.class));
        finish();
    }

    @Override
    protected void onConferenceTerminated(HashMap<String, Object> extraData) {
        // Host ended the meeting
        startActivity(new Intent(this, MyMeetingEndedActivity.class));
        finish();
    }
}
```

Then launch `MyMeetActivity` instead of `ConvayMeetActivity`.



## Variable Details

- `authToken`: Enterprise authentication token used for start meeting flow.
- `meetingLink`: Full join URL for the meeting (include protocol and path).
- `userInfo`: Optional user profile (name, email, avatar) shown in the meeting UI.
- `audioMuted` / `videoMuted`: Initial media state when the meeting starts.


## Feature Flags

Below are the main feature flags you can pass via `setFeatureFlag` on `ConvayMeetConferenceOptions.Builder`.
Unless noted otherwise, the default is **enabled (true)**.

### Core flow

- `startpage.enabled`: Required for start meeting flow (token-based).
- `joinpage.enabled`: Required for join meeting flow (link-based).
- `unique-participant-join.enabled`: Allow duplicate JWT session handling (replacement / older session leaves). Requires the corresponding JWT claim when enabled. Default: disabled (false).

### UI & layout

- `filmstrip.enabled`: Show filmstrip with participant thumbnails.
- `tile-view.enabled`: Allow tile/grid view layout.
- `fullscreen.enabled`: Use immersive full-screen mode (hides system bars in conference).
- `toolbox.enabled`: Show the bottom toolbox (main control bar).
- `toolbox.alwaysVisible`: Keep toolbox always visible (no auto-hide). Default: false.
- `active-speaker-name.enabled`: Show active-speaker name label on large video / speaker view.


### Media controls

- `audio-mute.enabled`: Show audio mute/unmute button.
- `video-mute.enabled`: Show video mute/unmute button.
- `audio-only.enabled`: Show Audio Only toggle in overflow menu.
- `resolution`: Override local / max remote resolution (e.g. `180`, `360`, `720`) as configured by the SDK.
- `video-share.enabled`: Show the video share button.
- `overflow-menu.enabled`: Show the overflow (“more”) menu button.

### Screen sharing

- `android.screensharing.enabled`: Enable Android screen sharing.
- `ios.screensharing.enabled`: Enable iOS screen sharing. Default: disabled.
- `screenshare.landscape.enabled`: Lock to landscape while any participant is screen sharing. Default: disabled (false).

### Recording & streaming

- `recording.enabled`: Enable recording controls. Default: auto-detected.
- `ios.recording.enabled`: Enable iOS-specific recording UI. Default: disabled.
- `live-streaming.enabled`: Enable live streaming. Default: auto-detected.

### Participants & reactions

- `participants.enabled`: Enable participants list / pane.
- `raise-hand.enabled`: Enable Raise Hand feature.
- `reactions.enabled`: Enable emoji reactions.
- `kick-out.enabled`: Allow moderators to remove participants.
- `breakout-rooms.enabled`: Show Breakout Rooms button in the overflow menu.
- `replace.participant`: Join using replace-participant semantics (advanced use cases).

### Communication & collaboration

- `chat.enabled`: Enable in-meeting chat and related UI (including unread indicators when enabled).
- `invite.enabled`: Enable invite UI.
- `invite-dial-in.enabled`: Enable dial-in invite UI.
- `add-people.enabled`: Enable Add People entry point.
- `close-captions.enabled`: Enable subtitles / closed captions.

### Notifications

- `notifications.enabled`: Show in-meeting notifications (host / moderator only when enabled). Default: enabled.

### Picture-in-Picture & navigation

- `pip.enabled`: Enable Picture-in-Picture (where supported). Default: auto-detected.
- `pip-while-screen-sharing.enabled`: Show PiP button while screen sharing. Default: disabled (false).

### Meeting security & lobby

- `meeting-password.enabled`: Show meeting password button (password dialog may still appear if the room has a password).
- `lobby-mode.enabled`: Enable lobby mode controls.
- `security-options.enabled`: Show Security Options button.
- `unsaferoomwarning.enabled`: Enable unsafe-room warning. Default: disabled (false).

### Misc & settings

- `audio-focus.disabled`: Do **not** request audio focus (for apps managing audio themselves). Default: false.
- `help.enabled`: Show Help button.
- `settings.enabled`: Show Settings entry in the UI.
- `calendar.enabled`: Calendar integration. Default: enabled on Android.
- `server-url-change.enabled`: Allow changing server URL in UI.

### Mobile-only

- `call-integration.enabled`: Enable CallKit / ConnectionService integration.
- `car-mode.enabled`: Enable car mode UI on mobile.

### Advanced join behavior

- `unique-participant-join.enabled`: Allow duplicate JWT session handling (replacement / older session leaves). Requires the corresponding JWT claim when enabled. Default: disabled (false).


---

## 🧪 Sample Android App

Reference implementation:
https://github.com/Synesis-IT-PLC/convay-meet-sdk-samples/tree/main/android