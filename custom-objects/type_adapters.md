# TypeAdapters

Hive supports all primitive types, `List`, `Map`, `DateTime` and `Uint8List`. If you want to store other objects, you have to register a `TypeAdapter` which converts the object from and to binary form.

You can either write a `TypeAdapter` yourself or generate it. Most of the time the generated adapter will perform really good. Sometimes there are small things you can improve with a manually written adapter.

## Register Adapter

When you want Hive to use a `TypeAdapter`, you have to register it. Two things are needed for that: An instance of the adapter and a `typeId`. Every type has a unique `typeId` which is used to find the correct adapter when a value is brought back from disk. All `typeId`s between 0 and 223 are allowed.

```dart
Hive.registerAdapter(MyObjectAdapter());
```

?> Make sure to use `typeId`s consistently. Your changes have to be compatible to previous versions of the box.

?> It's recommended to register all `TypeAdapter`s before opening any boxes.

```dart:dart:500px
import 'package:hive/hive.dart';

class User {
  String name;

  User(this.name);

  @override
  String toString() => name; // Just for print()
}

void main() async {
  // Register Adapter
  Hive.registerAdapter(UserAdapter()); 

  var box = await Hive.openBox<User>('userBox');

  box.put('david', User('David'));
  box.put('sandy', User('Sandy'));

  print(box.values);
}

// Can be generated automatically
class UserAdapter extends TypeAdapter<User> {
  @override
  final typeId = 0;

  @override
  User read(BinaryReader reader) {
    return User(reader.read());
  }

  @override
  void write(BinaryWriter writer, User obj) {
    writer.write(obj.name);
  }
}
```