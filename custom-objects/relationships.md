# Relationships

Sometimes your models are connected with each other. The following person class has a list of other persons called "friends". There could also be a list of other objects like "pets".

```dart
class Person extends HiveObject {
  String name;
  
  int age;
  
  List<Person> friends;
  
  Person(this.name, this.age);
}
```

You could just use a regular list to store the persons, but updating a person would be rather complicated because the person objects would be stored redundantly.

## HiveLists

HiveLists provide an easy way to solve the problem above. They allow you to store a "link" to the actual object.

```dart:dart:500px
import 'package:hive/hive.dart';

void main() async {
  Hive.registerAdapter(PersonAdapter());
  var persons = await Hive.openBox<Person>('personsWithLists');
  persons.clear();
  
  var mario = Person('Mario');
  var luna = Person('Luna');
  var alex = Person('Alex');
  persons.addAll([mario, luna, alex]);
  
  mario.friends = HiveList(persons); // Create a HiveList
  mario.friends.addAll([luna, alex]); // Update Mario's friends
  mario.save(); // make persistent the change,
  print(mario.friends);
  
  luna.delete(); // Remove Luna from Hive
  print(mario.friends); // HiveList updates automatically
}

@HiveType()
class Person extends HiveObject {
  @HiveField(0)
  String name;

  @HiveField(1)
  HiveList friends;

  Person(this.name);

  String toString() => name; // For print()
}

class PersonAdapter extends TypeAdapter<Person> {
  @override
  final typeId = 0;

  @override
  Person read(BinaryReader reader) {
    return Person(reader.read())..friends = reader.read();
  }

  @override
  void write(BinaryWriter writer, Person obj) {
    writer.write(obj.name);
    writer.write(obj.friends);
  }
}
```

First, we store the three persons, Mario, Luna, and Alex in the `persons` box. HiveLists can only be used with objects which are currently in a box.

Next, we create a `HiveList` which contains Mario's friends. The `HiveList` constructor needs the `HiveObject`, which will contain the list. The list must not be moved to another `HiveObject`. The second parameter is the box, which contains the items of the list.

When you delete an object from a box, it is also deleted from all `HiveLists`. If you delete an object from a `HiveList`, it still remains in the box.

