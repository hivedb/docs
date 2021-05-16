# Inheritance

Always register the base clase for last. Example


```dart
import 'package:hive/hive.dart';

part 'models.g.dart';

@HiveType(typeId: 0)
class Person extends HiveObject {
  @HiveField(0)
  List<Pet> pets = [];

  @HiveField(1)
  String name = "";

  @HiveField(2)
  Cat cat = Cat();

  @HiveField(3)
  Dog dog = Dog();

  Person();
}

@HiveType(typeId: 1)
class Pet extends HiveObject {
  @HiveField(0)
  String name = "";
}

@HiveType(typeId: 2)
class Dog extends Pet {
  @HiveField(1)
  String someDogField = "";

  Dog() : super();
}

@HiveType(typeId: 3)
class Cat extends Pet {
  @HiveField(1)
  String someCatField = "";

  Cat() : super();
}


Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Hive.initFlutter();
  Hive.registerAdapter<Person>(PersonAdapter());
  Hive.registerAdapter<Dog>(DogAdapter());
  Hive.registerAdapter<Cat>(CatAdapter());
  Hive.registerAdapter<Pet>(PetAdapter()); # FOR LAST !!!

  Box<Person> boxPersons = await Hive.openBox<Person>("persons");
  if (boxPersons.values.isEmpty) {
    print("Adding a new person");

    Person person = Person()
      ..name = "person 1"
      ..pets = [
        Pet()..name = "pet 1",
        Pet()..name = "pet 2",
        Pet()..name = "pet 3",
      ]
      ..dog = (Dog()
        ..name = "doggy"
        ..someDogField = "waw")
      ..cat = (Cat()
        ..name = "pussy"
        ..someCatField = "meau");

    boxPersons.put(0, person);
  } else {
    print(boxPersons.values);
  }

  runApp(MyApp());
}
```
