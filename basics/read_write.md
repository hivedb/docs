# Reading and Writing

## Read

Reading from a box is very straightforward:

```dart
var box = Hive.box('myBox');

String name = box.get('name');

DateTime birthday = box.get('birthday');
```

If the key does not exist, `null` is returned. Optionally you can specify a `defaultValue` that is returned in case the key does not exist.

```dart
double height = box.get('randomKey', defaultValue: 17.5);
```

Lists returned by `get()` are always of type `List<dynamic>` \(Maps of type `Map<dynamic, dynamic>`\). Use `list.cast<SomeType>()` to cast them to a specific type.

## Every object only exists once

You always get the same instance of an object from a specific key. It does not matter for primitive values since primitives are immutable, but it is essential for all other objects.

Here is an example:

```dart:dart:300px
import 'package:hive/hive.dart';

void main() async {
  var box = await Hive.openBox('someBox');

  var initialList = ['a', 'b', 'c'];
  box.put('myList', initialList);

  var myList = box.get('myList');
  myList[0] = 'd';

  print(initialList[0]); // d
}
```

The `initialList` and `myList` are the same instance and share the same data.

In the sample above, only the cached object has been changed and not the underlying data. To persist the change, `box.put('myList', myList)` needs to be called.


## Write

Writing to a box is almost like writing to a map. All keys have to be ASCII Strings with a max length of 255 chars or unsigned 32 bit integers.

```dart
var box = Hive.box('myBox');

box.put('name', 'Paul');

box.put('friends', ['Dave', 'Simon', 'Lisa']);

box.put(123, 'test');

box.putAll({'key1': 'value1', 42: 'life'});
```

You may wonder why writing works without async code. This is one of the main strengths of Hive.  
The changes are written to the disk as soon as possible in the background but all listeners are notified immediately. If the async operation fails \(which it should not\), all listeners are notified again with the old values.  
If you want to make sure that a write operation is successful, just await its `Future`.

This works differently for [lazy boxes](../advanced/lazy_box.md): As long as the `Future` returned by `put()` did not finish, `get()` returns the old values \(or `null` if it doesn't exist\).

The following code shows the difference:

```dart
var box = await Hive.openBox('box');

box.put('key', 'value');
print(box.get('key')); // value

var lazyBox = await Hive.openLazyBox('lazyBox');

var future = lazyBox.put('key', 'value');
print(lazyBox.get('key')); // null

await future;
print(lazyBox.get('key')); // value
```

## Delete

If you want to change an existing value, you can either override it using for example `put()` or delete it:

```dart:dart:260px
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

### Write null vs delete

Writing `null` is **NOT** the same as deleting a value.

```dart:dart:300px
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