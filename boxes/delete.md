# Delete

If you want to change an existing value, you can either override it using for example `put()` or delete it:

```dart:dart:300px
import 'package:hive/hive.dart';

void main() async {
  var box = await Hive.openBox('deleteExample');

  box.putAll({'key1': 'good', 'key2':'evil'});
  print(box.values);

  box.delete('key2');
  print(box.values);
}
```

If the key does not exist, no disk access is needed and the returned `Future` finishes immediately.

## null vs delete

Writing `null` is **NOT** the same as [deleting](delete.md) a value.

```dart:dart:350px
import 'package:hive/hive.dart';

void main() async {
  var box = await Hive.openBox('writeNullBox');

  box.put('key', 'value');

  box.put('key', null);
  print(box.containsKey('key'));

  box.delete('key');
  print(box.containsKey('key'));
}
```