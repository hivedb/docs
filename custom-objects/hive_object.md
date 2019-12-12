# HiveObject

When you store custom objects in Hive you can extend `HiveObject` to manage your objects easily.`HiveObject` provides the key of your object and useful helper methods like `save()` or `delete()`.

Here is an example how to use `HiveObject`:

```dart
@HiveType()
class Person extends HiveObject {
  @HiveField(0);
  String name;

  @HiveField(1);
  int age;
}
```

```dart
var box = Hive.box('persons');

var person = Person()
  ..name = 'Lisa'
  ..age = 32;

box.add(person); // Store this object for the first time

print('New key of Lisa: ' + person.key);

person.age = 35;
person.save(); // Update object

person.delete(); // Remove object from Hive
```

{% hint style="info" %}
You also need to extend `HiveObject` if you want to use queries.
{% endhint %}

