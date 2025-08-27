
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
