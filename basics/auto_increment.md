# Auto increment & indices

We already know that Hive supports unsigned integer keys. You can use auto-increment keys if you like. This is very useful for storing and accessing multiple objects. You can use a Box like a list.

```dart:dart:400px
import 'package:hive/hive.dart';

void main() async {
  var friends = await Hive.openBox('friends');
  friends.clear();

  friends.add('Lisa');            // index 0, key 0
  friends.add('Dave');            // index 1, key 1
  friends.put(123, 'Marco');      // index 2, key 123

  print(friends.getAt(0));
  print(friends.get(0));
  
  print(friends.getAt(1));
  print(friends.get(1));
  
  print(friends.getAt(2));
  print(friends.get(123));
}
```

There are also `getAt()`, `putAt()` and `deleteAt()` methods to access or change values by their index.

By default, String keys are sorted lexicographically and they have also indices.

?> Even if you only use auto increment keys, you should not rely on keys and indices being the same.

