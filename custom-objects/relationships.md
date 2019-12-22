# Relationships

Sometimes your models are connected with each other. The following person class has a list of other persons called "friends". There could also be a list of other objects like "pets".

```dart
class Person extends HiveObject {
  String name;

  int age;

  HiveList<Person> friends;

  Person(this.name, this.age);
}
```

You could just use a regular list to store the persons, but updating a person would be rather complicated because the person objects would be stored redundantly.

## HiveLists

HiveLists provide an easy way to solve the problem above. They allow you to store a "link" to the actual object.

```dart
void main() {
  var persons = Hive.box<Person>('persons');

  var mario = Person('Mario', 27);
  var luna = Person('Luna', 34);
  var alex = Person('Alex', 16);
  persons.addAll([mario, luna, alex]);

  mario.friends = HiveList(mario, persons); // Create a HiveList
  mario.friends.addAll([luna, alex]); // Add Luna and Alex to Mario's friends
  print(mario.friends); // [Luna, Alex]

  luna.delete(); // Remove Luna from Hive
  print(mario.friends); // [Alex] (HiveList updates automatically)
}
```

First, we store the three persons, Mario, Luna, and Alex in the `persons` box. HiveLists can only be used with objects which are currently in a box.

Next, we create a `HiveList` which contains Mario's friends. The `HiveList` constructor needs the `HiveObject`, which will contain the list. The list must not be moved to another `HiveObject`. The second parameter is the box, which contains the items of the list.

When you delete an object from a box, it is also deleted from all `HiveLists`. If you delete an object from a `HiveList`, it still remains in the box.

{% hint style="warning" %}
It is important to use `HiveList` as field type in models. `List friends = HiveList(...)` does not work correctly.
{% endhint %}

