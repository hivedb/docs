# Read

Reading from a box is very straightforward:

```dart
var box = Hive.box('myBox');

String name = box.get('name');

DateTime birthday = box.get('birthday');
```

If the key does not exist, `null` is returned. Optionally you can specify a `defaultValue` which will be returned in case the key does not exist.

```dart
double height = box.get('randomKey', defaultValue: 17.5);
```

Lists returned by `get()` are always of type `List<dynamic>` \(Maps of type `Map<dynamic, dynamic>`\). Use `list.cast<SomeType>()` to cast them to a specific type.

## Related

[Watch changes](watch_changes.md) to learn more about box.watch() and how to listen for changes using a `Stream`.
[Hive & Flutter](../best-practices/hive_and_flutter.md) for `WatchBoxBuilder`, a Widget that responds when a box value changes.
