# Manage Multiple App Flavors
> manage (development, production) apps using flutter_flavorizr

## Step 1: Add flutter_flavorizr package in dev_dependencies
```bash

flutter_flavorizr: ^2.4.1

```

## Step 2: Add a flavorizr section
> At the bottom of pubspec.yaml aligned with flutter you can add many flavours you want

```bash
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
```bash

dart run flutter_flavorizr

```

## Step 4: Command to run in debug, profile, release mode
> Debug mode
> if not using multiple main.dart (like: main_dev.dart or main_prod.dart)
```bash
flutter run --flavor dev

flutter run --flavor prod
```
> If you are using multiple main, then you have setup accordingly then use as :
```bash
flutter run --flavor dev -t lib/main_dev.dart

flutter run --flavor prod -t lib/main_dev.dart
```
> Profile Mode
```bash
flutter run --flavor dev --profile 
flutter run --flavor prod --profile
```
> Release Mode
```bash
flutter run --flavor dev --release 
flutter run --flavor prod --release
```

## Step 5: Command to create builds
### Android
> Debug APK
```bash

flutter build apk --flavor dev

```
> Release APK
```bash

flutter build apk --flavor prod

```
> Release AAB (Play Store)
```bash

flutter build appbundle --flavor prod

```

### IOS
> Debug
```bash

flutter build ios --flavor dev

```
> Release
```bash

flutter build ios --flavor prod

```
<br> <br>