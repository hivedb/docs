# HiveObject

When you store custom objects in Hive you can extend `HiveObject` to manage your objects easily.`HiveObject` provides the key of your object and useful helper methods like `save()` or `delete()`.

Here is an example how to use `HiveObject`:

```dart:dart:650px
import 'package:hive/hive.dart';

void main() async {
  Hive.registerAdapter(PersonAdapter(), 0);
  var persons = await Hive.openBox('persons');

  var person = Person()
    ..name = 'Lisa';

  persons.add(person); // Store this object for the first time

  print('Number of persons: ${persons.length}');
  print("Lisa's first key: ${person.key}");

  person.name = 'Lucas';
  person.save(); // Update object

  person.delete(); // Remove object from Hive
  print('Number of persons: ${persons.length}');

  persons.put('someKey', person);
  print("Lisa's second key: ${person.key}");
}

@HiveType()
class Person extends HiveObject {
  @HiveField(0)
  String name;
}

class PersonAdapter extends TypeAdapter<Person> {
  @override
  Person read(BinaryReader reader) {
    return Person()..name = reader.read();
  }

  @override
  void write(BinaryWriter writer, Person obj) {
    writer.write(obj.name);
  }
}
```

?> You also need to extend `HiveObject` if you want to use queries.

