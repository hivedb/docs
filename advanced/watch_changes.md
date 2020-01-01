# Watch changes

If you want to get notified about changes in a box, you can subscribe to the `Stream` returned by `box.watch()`. Every `put()`, `putAll()`, `delete()` and `deleteAll()` operation is broadcasted to that stream.

In Flutter apps you can rebuild widgets every time the box changes.

```dart:dart:350px
import 'package:hive/hive.dart';

void main() async {
  var box = await Hive.openBox('watchChangesBox');

  box.watch().listen((event) {
    if (event.deleted) {
      print('${event.key} deleted');
    } else {
      print('${event.key} assigned to ${event.value}');
    }
  });

  box.put('someKey', 123);
  box.delete('someKey');
}
```

If you provide the `key` parameter, you are only notified about changes of the specified key.

```dart
box.watch(key: 'someKey').listen((event) {
    // ...
});
```

