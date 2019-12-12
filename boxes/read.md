# Read

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

You always get the same instance of an object from a specific key. It does not matter for primitive values since primitives are immutable, but it is essentialfor all other objects.

Here is an example:

```dart
var box = Hive.box('someBox');

var initialList = ['a', 'b', 'c'];
box.put('myList', initialList);

var myList = box.get('myList');
myList[0] = 'd';

print(initialList[0]); // d
```

The `initialList` and `myList` are the same instance and share the same data.

In the sample above, only the cached object has been changed and not the underlying data. To persist the change, `box.put('myList', myList)` needs to be called.

