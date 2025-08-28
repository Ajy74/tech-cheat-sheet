# Flutter Version Management

You can manage multiple Flutter SDK versions on a single system using the **FVM (Flutter Version Management)** package.  
This is useful when working on different projects that require different Flutter versions.

## Steps to follow 
```bash
dart pub global activate fvm

```

>Note: If you encounter an error like fvm: command not found, you may need to add the Dart pub cache bin to your PATH: <br>
>Windows: C:\Users\<YourUsername>\AppData\Local\Pub\Cache\bin  <br>
>macOS/Linux: ~/.pub-cache/bin
```bash
fvm --version  // to check fvm version
fvm install 3.29.0  // intall flutter desired version 
```

> Go inside in your Flutter project and run this command to configure
```bash

fvm use 3.29.0

```
> Now you all set to run flutter command using fvm  <br>
> Note : to use the flutter version you have set for project usinf fvm , you must prefix fvm before flutter commands like :
```bash
fvm flutter doctor 
fvm flutter run --debug
fvm flutter run --profile
fvm flutter build apk --release
```
<br> <br>
