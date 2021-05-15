# Quick Start

## Add to project

Add the following to your `pubspec.yaml`:

```version
dependencies:
  hive: ^[version]
  hive_flutter: ^[version]

dev_dependencies:
  hive_generator: ^[version]
  build_runner: ^[version]
```


## Initialize

Initializes Hive with a valid directory in your app files. You can also provide a subdirectory:

```dart
# import both package
import 'package:hive/hive.dart';
import 'package:hive_flutter/hive_flutter.dart';

await Hive.initFlutter();
```

?> Use `Hive.init()` for non-Flutter apps.

## Open a Box

All of your data is stored in boxes.

```dart
var box = await Hive.openBox('testBox');
```

?> You may call `box('testBox')` to get the singleton instance of an already opened box.

## Read & Write

Hive supports all primitive types, `List`, `Map`, `DateTime`, `BigInt` and `Uint8List`. Any object can be stored using [TypeAdapters](custom-objects/generate_adapter.md).

```dart:dart:300px
import 'package:hive/hive.dart';

void main() async {
  //Hive.init('somePath') -> not needed in browser

  var box = await Hive.openBox('testBox');

  box.put('name', 'David');
  
  print('Name: ${box.get('name')}');
}
```

## Video Tutorial

Learn the basics of using Hive in this well-made tutorial by Reso Coder.

<div class="container">
  <iframe id="ytplayer" type="text/html" data-src="https://www.youtube.com/embed/R1GSrrItqUs" class="video"/>
</div>

