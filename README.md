---
description: Lightweight and blazing fast key-value database written in pure Dart.
---

# Hive Documentation

## Add Hive to project

Add the following to your `pubspec.yaml`. Use the latest version instead of `[version]`.

[![Core version](https://img.shields.io/pub/v/hive?label=hive)](https://pub.dev/packages/hive) [![Generator version](https://img.shields.io/pub/v/hive_generator.svg?label=hive_flutter)](https://pub.dev/packages/hive_flutter) [![Flutter version](https://img.shields.io/pub/v/hive_generator.svg?label=hive_generator)](https://pub.dev/packages/hive_generator) [![Build runner version](https://img.shields.io/pub/v/build_runner.svg?label=build_runner)](https://pub.dev/packages/build_runner)

```yaml
dependencies:
  hive: ^[version]
  hive_flutter: ^[version]

dev_dependencies:
  hive_generator: ^[version]
  build_runner: ^[version]
```

## Initialize

Before you can use Hive in your app, you must initialize it. This only has to be done once.

Give Hive a directory where it can store its files. It is recommended to use an empty directory. Each box will have it's own `.hive` file in the Hive-home directory.

```dart
Hive.init('path/to/hive');
```

If you use a directory outside your app files, make sure to request runtime permission on Android.

_In the browser you don't have to call `Hive.init()`._

## Open a Box

All of your data is stored in boxes.

```dart
var box = await Hive.openBox('testBox');
```

{% hint style="info" %}
You may call `box('testBox')` to get the singleton instance of an already opened box. This will save one async call.
{% endhint %}

## Read & Write

Hive supports all primitive types, `List`, `Map`, `DateTime` and `Uint8List`. Any object can be can stored using [TypeAdapters](custom-objects/generate_adapter.md).

```dart
box.put('name', 'David');

var name = box.get('name');

print('Name: $name');
```

## Video Tutorial

{% embed url="https://www.youtube.com/watch?v=R1GSrrItqUs" caption="Learn the basics of using Hive in this well made tutorial by Reso Coder." %}

