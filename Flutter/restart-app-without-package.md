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
>📝 How It Works for me <br>
>**entryPoint()** initializes Hive, dependency locator, and runs the app. <br>
>**restartApp()**  <br>
> --> Navigates to the root route (/) using GoRouter. <br>
> --> Adds a short delay. <br> 
> --> Resets GetIt dependency container. <br>
> --> Re-initializes dependencies with diSetup(). <br> 
>**MyApp** is rebuilt with fresh providers and a unique GlobalKey.
<br> <br>