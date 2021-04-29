# Generate adapter

The [hive\_generator](https://pub.dev/packages/hive_generator) package can automatically generate `TypeAdapter`s for almost any class.

1. To generate a `TypeAdapter` for a class, annotate it with `@HiveType` and provide a `typeId` (between 0 and 223)
2. Annotate all fields which should be stored with `@HiveField`
3. Run build task `flutter packages pub run build_runner build`
4. [Register](custom-objects/type_adapters.md) the generated adapter

### Example

Given a library `person.dart` with a `Person` class annotated with `@HiveType` with a **unique** `typeId` argument:

```dart
import 'package:hive/hive.dart';

part 'person.g.dart';

@HiveType(typeId: 1)
class Person {
  @HiveField(0)
  String name;

  @HiveField(1)
  int age;

  @HiveField(2)
  List<Person> friends;
}
```

As you can see, each field annotated with `@HiveField` has a **unique** number \(unique per class\). These field numbers are used to identify the fields in the Hive binary format, and should not be changed once your class is in use.

_Field numbers can be in the range 0-255_.

The above code generates an adapter class called `PersonAdapter`. You can change that name with the optional `adapterName` parameter of `@HiveType`.

## Updating a class

If an existing class needs to be changed – for example, you'd like the class to have a new field – but you'd still like to read objects written with the old adapter, don't worry! It is straightforward to update generated adapters without breaking any of your existing code. Just remember the following rules:

* Don't change the field numbers for any existing fields.
* If you add new fields, any objects written by the "old" adapter can still be read by the new adapter. These fields are just ignored. Similarly, objects written by your new code can be read by your old code: the new field is ignored when parsing.
* Fields can be renamed and even changed from public to private or vice versa as long as the field number stays the same.
* Fields can be removed, as long as the field number is not used again in your updated class.
* Changing the type of a field is not supported. You should create a new one instead.

## Enums

Generating an adapter for enums works almost as it does for classes:

```dart
@HiveType(typeId : 2)
enum HairColor {
  @HiveField(0)
  brown,

  @HiveField(1)
  blond,

  @HiveField(2)
  black,
}
```

For updating the enum, the same rules apply as above.

## Default value

You can provide default values to properties and fields easily by providing `defaultValue` argument to `@HiveField` annotation.

```dart
@HiveType(typeId: 2)
class Customer {
  @HiveField(1, defaultValue: 0.0)
  double balance;
}
```

You can also provide default value for enum types by setting `defaultValue` to `true`. If you have not set default value for enum types the first value will be used as default value instead.

```dart
@HiveType(typeId : 2)
enum HairColor {
  @HiveField(0)
  brown,

  @HiveField(1)
  blond,

  @HiveField(2, defaultValue: true)
  black,
}
```
