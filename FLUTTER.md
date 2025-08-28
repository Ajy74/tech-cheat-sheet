
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


# Flutter Version Management

You can manage multiple Flutter SDK versions on a single system using the **FVM (Flutter Version Management)** package.  
This is useful when working on different projects that require different Flutter versions.

## Steps to follow 
```
dart pub global activate fvm

```

>Note: If you encounter an error like fvm: command not found, you may need to add the Dart pub cache bin to your PATH:
>Windows: C:\Users\<YourUsername>\AppData\Local\Pub\Cache\bin
>macOS/Linux: ~/.pub-cache/bin
```
fvm --version  // to check fvm version
fvm install 3.29.0  // intall flutter desired version 
```

> Go inside in your Flutter project and run thhis command to configure
```

fvm use 3.29.0

```
> Now you all set to run flutter command using fvm 
> Note : to use the flutter version you have set for project usinf fvm , you must prefix fvm before flutter commands like :
```
fvm flutter doctor 
fvm flutter run --debug
fvm flutter run --profile
fvm flutter build apk --release
```

# Restart Flutter App Without Package

There is **no built-in way** in Flutter to restart the app programmatically.

- Using `exit(0)` will **close** your Flutter app.
  - ✅ Works on **Android**  
  - ❌ Not allowed on **iOS** (Apple rejects apps that programmatically exit)
  - ⚠️ Even on Android, it forces the user to reopen the app manually → **bad UX**

## 💡 Alternative Solution (Without Package)
Instead of killing the app, you can simulate a "restart" by rebuilding the entire widget tree from the root.  
One way to achieve this is by using a **unique `Key`** on the root widget.

### Here’s the **tested working code** from one of my project:

```dart
void main() async {
  await entryPoint();
}

Future<void> entryPoint() async {
  WidgetsFlutterBinding.ensureInitialized();

  final dir = await getApplicationDocumentsDirectory();
  Hive.init(dir.path);

  diSetup();
  runApp(MyApp(key: GlobalKey(debugLabel: "MyAppKey")));
}

Future<void> restartApp() async {
  if (rootNavigatorKey.currentContext != null) {
    GoRouter.of(rootNavigatorKey.currentContext!).go("/");

    // Small delay to ensure navigation completes
    await Future.delayed(const Duration(seconds: 1));

    // Reset dependencies (GetIt in my case)
    await GetIt.instance.reset();
  }

  diSetup(); // re-initialize dependencies
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    DeviceDimens(context);

    return MultiBlocProvider(
      key: GlobalKey(debugLabel: "MultiBlocProviderKey"),
      providers: [
        BlocProvider(create: (context) => InternetCubit()),
        BlocProvider(create: (context) => getIt<HomeCubit>()),
        BlocProvider(create: (context) => getIt<VideoPlayerCubit>()),
      ],
      child: Builder(builder: (context) {
        return MediaQuery(
          data: MediaQuery.of(context).copyWith(
            textScaler: TextScaler.noScaling,
          ),
          child: MaterialApp.router(
            debugShowCheckedModeBanner: false,
            title: 'Demo Course Video',
            theme: ThemeData(
              colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
              useMaterial3: true,
            ),
            routerConfig: AppRoutes.router,
          ),
        );
      }),
    );
  }
}
```
>📝 How It Works for me
>**entryPoint()** initializes Hive, dependency locator, and runs the app.
>**restartApp()**
> Navigates to the root route (/) using GoRouter.
> Adds a short delay.
> Resets GetIt dependency container.
> Re-initializes dependencies with diSetup().
>**MyApp** is rebuilt with fresh providers and a unique GlobalKey.



