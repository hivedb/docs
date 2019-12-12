# Watch changes

If you want to get notified about changes in a box, you can subscribe to the `Stream` returned by `box.watch()`. Every `put()`, `putAll()`, `delete()` and `deleteAll()` operation is broadcasted to that stream.

In Flutter apps you can rebuild widgets every time the box changes.

```dart
var box = Hive.box('myBox');

box.watch().listen((event) {
  if (event.deleted) {
    print('${event.key} has been deleted');
  } else {
    print('${event.key} is now assigned to ${event.value}');
  }
});

box.put('someKey', 123); // > someKey is now assigned to 123
box.delete('someKey'); // > someKey has been deleted
```

If you specify the `key` parameter, you are only notified about changes of this key.

```dart
box.watch(key: 'someKey').listen((event) {
    // ...
});
```

