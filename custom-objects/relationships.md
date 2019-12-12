# Relationships

Sometimes your models are connected with each other 

```dart
class Person extends HiveObject {
  String name;
  
  int age;
  
  List<Person> get friends => getHiveList('friends');
}
```

