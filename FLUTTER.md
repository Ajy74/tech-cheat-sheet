
# Remove Flutter’s Default Native Android Splash Screen (Android)

By default, Flutter apps on Android show a native splash screen (usually a white background or app icon background) before the first Flutter frame is drawn.
If you want to skip this splash flicker and directly show your custom Flutter splash (or first screen), follow these steps:

## ✅ Step 1: Update styles.xml
> Path: android/app/src/main/res/values/styles.xml
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- Default Flutter launch theme -->
    <style name="LaunchTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/launch_background</item>
    </style>

    <!-- Transparent theme to skip default splash -->
    <style name="Theme.App.Transparent" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowIsTranslucent">true</item>
        <item name="android:windowNoTitle">true</item>
        <item name="android:windowFullscreen">true</item>
    </style>
</resources>
```

## ✅ Step 2: Update AndroidManifest.xml
> Find the application block in your manifest and replace the theme:
```

android:theme="@style/Theme.App.Transparent">   <!-- Updated theme as-->

```

## ✅ Step 3: Add AppCompat Dependency
> Path: android/app/build.gradle.kts
```
dependencies {
    implementation("androidx.appcompat:appcompat:1.4.0")
}
```
<br> <br>

# Manage Multiple App Flavors
> manage (development, production) apps using flutter_flavorizr

## Step 1: Add flutter_flavorizr package in dev_dependencies
```

flutter_flavorizr: ^2.4.1

```

## Step 2: Add a flavorizr section
> At the bottom of pubspec.yaml aligned with flutter you can add many flavours you want

```
flutter:
  uses-material-design: true
  ....

flavorizr:
  flavors:
    dev:
      app:
        name: "My App Dev"
      android:
        applicationId: "com.example.dev"
      ios:
        bundleId: "com.example.dev"
    prod:
      app:
        name: "My App"
      android:
        applicationId: "com.example.app"
      ios:
        bundleId: "com.example.app"
```

## Step 3: Run this command to configure
> this will auto create some files and configurations for both Android and Ios based on your flavour configration.
> auto creates flavors.dart in lib folder.
> auto creates app.dart
```

dart run flutter_flavorizr

```

## Step 4: Command to run in debug, profile, release mode
> Debug mode
> if not using multiple main.dart (like: main_dev.dart or main_prod.dart)
```
flutter run --flavor dev

flutter run --flavor prod
```
> If you are using multiple main, then you have setup accordingly then use as :
```
flutter run --flavor dev -t lib/main_dev.dart

flutter run --flavor prod -t lib/main_dev.dart
```
> Profile Mode
```
flutter run --flavor dev --profile 
flutter run --flavor prod --profile
```
> Release Mode
```
flutter run --flavor dev --release 
flutter run --flavor prod --release
```

## Step 5: Command to create builds
### Android
> Debug APK
```

flutter build apk --flavor dev

```
> Release APK
```

flutter build apk --flavor prod

```
> Release AAB (Play Store)
```

flutter build appbundle --flavor prod

```

### IOS
> Debug
```

flutter build ios --flavor dev

```
> Release
```

flutter build ios --flavor prod

```
