# Flutter Idle Tracker

## Overview
This package provides an **idle tracking service** for Flutter applications. It detects user inactivity and triggers a **warning dialog** before logging out inactive users. The service ensures accurate tracking by monitoring touch events, gestures, and keyboard inputs while handling app lifecycle changes.

## Features
✅ **Customizable timeout durations** (warning & logout)
✅ **Global activity tracking** using `GestureDetector`
✅ **Lifecycle-aware detection** (pauses when app is in the background)
✅ **Dialog-aware logic** (prevents multiple dialogs from appearing)
✅ **Seamless integration with any Flutter app**

## Installation
```yaml
dependencies:
  flutter:
    sdk: flutter
```

## Usage

### 1️⃣ Initialize Tracking in `main.dart`
Wrap your `MaterialApp` with `GestureDetector` to track global user activity:

```dart
return GestureDetector(
  onTap: IdleTrackerService().userActivityDetected,
  onPanDown: (_) => IdleTrackerService().userActivityDetected(),
  child: MaterialApp(
    home: HomeScreen(),
  ),
);
```

### 2️⃣ Start Tracking in `HomeScreen`

```dart
@override
void initState() {
  super.initState();
  WidgetsBinding.instance.addPostFrameCallback((_) {
    IdleTrackerService().setDialogState(false);
    IdleTrackerService().startTracking(
      onIdleCallback: () {
        NavigationService.showLogoutDialog();
      },
      onWarningCallback: () {
        logger.e("User inactivity warning");
      },
    );
  });
}

@override
void dispose() {
  IdleTrackerService().stopTracking();
  super.dispose();
}
```

### 3️⃣ Implement Logout Dialog

```dart
static void showLogoutDialog({BuildContext? ctx}) {
  if (ctx?.mounted ?? context.mounted) {
    IdleTrackerService().setDialogState(true);
    showDialog(
      context: ctx ?? context,
      barrierDismissible: false,
      barrierColor: Colors.black.withOpacity(0.5),
      builder: (context) {
        return Scaffold(
          backgroundColor: Colors.transparent,
          body: SafeArea(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                Container(
                  padding: const EdgeInsets.all(8),
                  margin: const EdgeInsets.all(8),
                  decoration: BoxDecoration(
                    color: Colors.white,
                    borderRadius: BorderRadius.circular(12),
                  ),
                  child: const SessionExpireDialog(),
                ),
              ],
            ),
          ),
        );
      },
    ).then((_) {
      IdleTrackerService().setDialogState(false);
    });
  }
}
```

## License
This project is licensed under the MIT License.

