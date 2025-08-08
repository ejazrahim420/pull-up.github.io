# 🚀 Installing eSIM Profiles in Flutter (Android Only) — Meet `android_esim_installer`

If you’ve ever tried installing eSIM profiles programmatically on Android, you know the pain.
Between **signature-only permissions**, **carrier restrictions**, and the mysterious `EuiccManager` API, it feels less like coding and more like solving a riddle.

That’s why I built **[`android_esim_installer`](https://pub.dev/packages/android_esim_installer)** — a **Flutter plugin** that makes installing eSIM profiles as easy as calling a single method.

---

## 🌟 Why This Plugin Exists

By default, Flutter has no native API for eSIM installation. While Android does have `EuiccManager`, it comes with some serious challenges:

* Requires **`WRITE_EMBEDDED_SUBSCRIPTIONS`**, a signature/privileged permission.
* Needs **carrier privileges** or a **system-signed app**.
* Handling callbacks and intents is **boilerplate-heavy**.

This plugin removes that boilerplate and gives you:

✅ A **simple Dart API**
✅ **Status callbacks** for real-time updates
✅ Clean integration with your Flutter project

---

## ⚠️ The Big Permission Gotcha

Before you get too excited — this **will not work** unless:

1. Your app is a **system/privileged app** signed with the platform key, **OR**
2. You have **carrier privileges** from the eUICC/carrier.

Also, your eSIM profiles must be **signed** by the carrier with your **package name or SHA-1 keys**.

If not, you’ll hit:

```
error:PERMISSION_DENIED
```

… and there’s no workaround — this is an Android security requirement.

---

## 📦 Setting It Up in Flutter

### 1️⃣ AndroidManifest Changes

Edit `android/app/src/main/AndroidManifest.xml`:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.example.app">

    <!-- Protected eSIM write permission -->
    <uses-permission
        android:name="android.permission.WRITE_EMBEDDED_SUBSCRIPTIONS"
        tools:ignore="ProtectedPermissions" />

    <!-- Declare and request custom LPA broadcast permission -->
    <permission
        android:name="com.your_package_name.lpa.permission.BROADCAST"
        android:protectionLevel="signature" />
    <uses-permission android:name="com.your_package_name.lpa.permission.BROADCAST" />

    <!-- Optional: declare telephony features -->
    <uses-feature android:name="android.hardware.telephony" android:required="false" />
    <uses-feature android:name="android.hardware.telephony.euicc" android:required="false" />
</manifest>
```

🔹 **Important:** Replace `com.your_package_name` with your real `applicationId` from `build.gradle`.

---

### 2️⃣ Gradle Requirements

In `android/app/build.gradle`:

```gradle
android {
  defaultConfig {
    minSdkVersion 28
  }
  compileSdkVersion 35
  targetSdkVersion 35
}
```

---

## 🚀 Installation

In `pubspec.yaml`:

```yaml
dependencies:
  android_esim_installer: ^1.0.2
```

Run:

```bash
flutter pub get
```

---

## 🛠 Usage Example

Installing an eSIM profile is as simple as:

```dart
import 'package:android_esim_installer/android_esim_installer.dart';

await AndroidEsimInstaller.install(
  activationCode: 'LPA:1$YOUR_SMDP_PLUS_ADDRESS$YOUR_MATCHING_ID',
  appPackageName: 'com.your_package_name',
  onInstalling: (msg) => print("Installing: $msg"),
  onResolving: (msg) => print("Resolving: $msg"),
  onSuccess: (msg) => print("Success: $msg"),
  onError: (msg) => print("Error: $msg"),
);
```

---

## 📡 Status Callbacks

You get real-time updates during installation:

| Callback       | Meaning                     |
| -------------- | --------------------------- |
| `onInstalling` | Download started            |
| `onResolving`  | Requires user action        |
| `onSuccess`    | eSIM installed successfully |
| `onError`      | Installation failed         |

---

## 🛠 How It Works Behind the Scenes

* Uses **`EuiccManager.downloadSubscription()`** to start the eSIM install.
* Listens for results via a **custom `BroadcastReceiver`**.
* Passes updates back to Flutter through method channels.
* Keeps everything **non-blocking** and **async**.

---

## 📌 Use Cases

* Carrier apps distributing eSIMs.
* Enterprise apps preloading eSIM profiles.
* OEM/system apps managing device connectivity.

---

## ⚠️ Limitations

* **Android only** (API 28+).
* Requires **carrier/system privileges**.
* Cannot bypass carrier security policies.

---

## 🎯 Final Thoughts

If you have the right privileges, `android_esim_installer` can save you **hours of work** and turn a messy native process into a **clean Flutter call**.

📦 **Get it here:** [Pub.dev - android\_esim\_installer](https://pub.dev/packages/android_esim_installer)
💻 **View Source:** *(Add your GitHub link here)*

---