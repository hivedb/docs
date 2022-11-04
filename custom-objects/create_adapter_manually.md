# Create adapter manually

Sometimes it might be necessary to create a custom `TypeAdapter`. You can do that by extending the `TypeAdapter` class. Make sure to specify the generic argument.

!> Test your custom `TypeAdapter` thoroughly. If it does not work correctly, it may corrupt your box.

It is very easy to implement a `TypeAdapter`. Keep in mind that `TypeAdapter`s have to be immutable! Here is the `DateTimeAdapter` used by Hive internally:

```dart
class DateTimeAdapter extends TypeAdapter<DateTime> {
  @override
  final typeId = 16;

  @override
  DateTime read(BinaryReader reader) {
    final micros = reader.readInt();
    return DateTime.fromMicrosecondsSinceEpoch(micros);
  }

  @override
  void write(BinaryWriter writer, DateTime obj) {
    writer.writeInt(obj.microsecondsSinceEpoch);
  }
}
```

!> As of Hive 1.3.0, all adapters require a `typeId` instance variable! The custom `typeId` must be an int between `0` and `223`.

The `typeId` instance variable assigns the number to be registered to that adapter. It has to be unique between all adapters.
The `read()` method is called when your object has to be read from the disk. Use the `BinaryReader` to read all the properties of your object. In the above sample, it is only an `int` containing `microsecondsSinceEpoch`.  
The `write()` method is the same just for writing the object to the disk.

?> Make sure, you read properties in the same order you have written them before.

