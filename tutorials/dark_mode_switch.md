# Dark Mode Switch Introduction

In this tutorial we will build a super simple app. It will have a single switch which can toggle between dark mode and light mode. We will use Hive to persist the switch state.

## Source Code Live Test

(No repository yet.)

Below you can find the final code and test the app.

(Refresh this page to test persistence)

```dart:flutter:500px
import 'package:flutter/material.dart';
import 'package:hive/hive.dart';
import 'package:hive_flutter/hive_flutter.dart';

const darkModeBox = 'darkModeTutorial';

void main() async {
  await Hive.initFlutter();
  await Hive.openBox(darkModeBox);
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ValueListenableBuilder(
      valueListenable: Hive.box(darkModeBox).listenable(),
      builder: (context, Box box, widget) {
        var darkMode = box.get('darkMode', defaultValue: false);
        return MaterialApp(
          themeMode: darkMode ? ThemeMode.dark : ThemeMode.light,
          darkTheme: ThemeData.dark(),
          home: Scaffold(
            body: Center(
              child: Switch(
                value: darkMode,
                onChanged: (val) {
                  box.put('darkMode', !darkMode);
                },
              ),
            ),
          ),
        );
      },
    );
  }
}
```

## Setup

First we create a new Flutter project:

```
flutter create dark_mode_switch
```

## Dependencies

Now we need to add Hive to the `pubspec.yaml` file in the project folder:

```yaml
name: dark_mode_switch

environment:
  sdk: ">=2.6.0 <3.0.0"

dependencies:
  flutter:
    sdk: flutter
  hive: ^1.3.0
  hive_flutter: ^0.3.0+1

flutter:
  uses-material-design: true
```

## Initialization

Now we can import `hive` and `hive_flutter` to initialize Hive.

```dart
import 'package:flutter/material.dart';
import 'package:hive/hive.dart';
import 'package:hive_flutter/hive_flutter.dart';

const darkModeBox = 'darkModeTutorial';

void main() async {
  await Hive.initFlutter();
  await Hive.openBox(darkModeBox);
  runApp(MyApp());
}
```

?> We open the box in the `main()` method, so we can later use `Hive.box()` and avoid dealing with async code.

## Structure of the app

The following is the main structure of our app. A Material themed app with a single `Switch` in the center. 

```dart:flutter:500px
import 'package:flutter/material.dart';

void main() async {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      darkTheme: ThemeData.dark(),
      home: Scaffold(
        body: Center(
          child: Switch(
            value: false,
            onChanged: (val) {},
          ),
        ),
      ),
    );
  }
}
```

## Persisting the Switch state

Now we read the `darkMode` entry from the box. We provide a `defaultValue` because the value will be `null` when the app starts for the first time.

Based on the `darkMode` value we set the `themeMode` of the `MaterialApp`.

When the user toggles the switch, we update the `darkMode` entry in the box.

```dart:flutter:700px
import 'package:flutter/material.dart';
import 'package:hive/hive.dart';
import 'package:hive_flutter/hive_flutter.dart';

const darkModeBox = 'darkModeTutorial';

void main() async {
  await Hive.initFlutter();
  await Hive.openBox(darkModeBox);
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  final box = Hive.box(darkModeBox);

  @override
  Widget build(BuildContext context) {
    var darkMode = box.get('darkMode', defaultValue: false);
    return MaterialApp(
      themeMode: darkMode ? ThemeMode.dark : ThemeMode.light,
      darkTheme: ThemeData.dark(),
      home: Scaffold(
        body: Center(
          child: Switch(
            value: darkMode,
            onChanged: (val) {
              box.put('darkMode', !darkMode);
            },
          ),
        ),
      ),
    );
  }
}
```

?> We can use `Hive.box(darkModeBox)` in `MyApp` because we opened the box before this widget is used.

When you run the example, you will notice that it does not work as intended. The reason is that we don't refresh our widgets based on the changed `darkMode` value.

## Refreshing

The last step is to refresh the app when necessary. The easiest way to refresh  widgets based on Hive changes is using `box.listenable()` and `ValueListenableBuilder`.

```dart:flutter:700px
import 'package:flutter/material.dart';
import 'package:hive/hive.dart';
import 'package:hive_flutter/hive_flutter.dart';

const darkModeBox = 'darkModeTutorial';

void main() async {
  await Hive.initFlutter();
  await Hive.openBox(darkModeBox);
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ValueListenableBuilder(
      valueListenable: Hive.box(darkModeBox).listenable(),
      builder: (context, Box box, widget) {
        var darkMode = box.get('darkMode', defaultValue: false);
        return MaterialApp(
          themeMode: darkMode ? ThemeMode.dark : ThemeMode.light,
          darkTheme: ThemeData.dark(),
          home: Scaffold(
            body: Center(
              child: Switch(
                value: darkMode,
                onChanged: (val) {
                  box.put('darkMode', !darkMode);
                },
              ),
            ),
          ),
        );
      },
    );
  }
}
```

!> In this example we rebuild the entire app when the `darkMode` value changes. You should only refresh the necessary widgets.
