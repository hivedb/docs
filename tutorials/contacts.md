# Contacts App

[![](https://img.shields.io/badge/author-%40Reprevise-blue)](https://github.com/Reprevise)

In this tutorial, we will be building a fully functional app that stores your contacts in under 230 lines of code!

We will be using model classes and enums, all of which Hive can store in it's databases. Pretty cool right? No more converting all of your models to JSON and your enums to strings. Let's get started!

## Dependencies

First, we need to define our dependencies. We will be using `dev_dependencies` to generate our [`TypeAdapters`](../custom-objects/type_adapters.md) automatically! Read more about generating adapters automatically [here](../custom-objects/generate_adapter.md).

```yaml
environment:
  sdk: '>=2.6.0 <3.0.0'

dependencies:
  flutter:
    sdk: flutter
  hive: ^1.3.0
  hive_flutter: ^0.3.0+1

dev_dependencies:
  hive_generator: ^0.7.0
  build_runner: ^1.7.2
  flutter_test:
    sdk: flutter
```

## Models and Enums

We need to define our models and enums. We're calling the model class `Contact` which stores the necessary information for a contact and one of the fields in the `Contact` model is an enum which is called `Relationship` which defines the relationship between the people.

Above every model and enum that you want stored in Hive, you need to put a `HiveType` annotation to show Hive that this is something you want stored.

Above every object in an enum and every field in a model, you need to add a `HiveField` annotation with a value. The values can be between 0 and 255 (0-255). Read more [here](../custom-objects/generate_adapter.md).

As you can see we also have a map which converts the `Relationship` enum to a string so we don't need to worry about conversion methods and messy `if statements`.

```dart
@HiveType(typeId: 1)
enum Relationship {
  @HiveField(0)
  Family,
  @HiveField(1)
  Friend,
}
const relationshipString = <Relationship, String>{
  Relationship.Family: "Family",
  Relationship.Friend: "Friend",
};

@HiveType(typeId: 0)
class Contact {
  @HiveField(0)
  String name;
  @HiveField(1)
  int age;
  @HiveField(2)
  Relationship relationship;
  @HiveField(3)
  String phoneNumber;

  Contact(this.name, this.age, this.phoneNumber, this.relationship);
}
```

## Initializing Hive and Adapters

Now we need to generate the adapters for Hive to use to read and write to the box and guess what, it's very easy! At the top of the file (just below the imports), add this line:

```dart
part 'main.g.dart';
```

Notice how you have the file name "main", then a "g" that stands for generated, and finally the file extension "dart". This is important so Dart knows that file is a part of `main.dart`.

Also notice how you have an error on that line. To get rid of it, run the following command:

```shell
flutter packages pub run build_runner build --delete-conflicting-outputs
```

That command generates the adapters for you, no work required! The `--delete-conflicting-outputs` option is useful if you're re-generating the files as it will delete them automatically. Otherwise, it will throw an error if you don't delete the files that have already been generated.

!> Do _not_ modify the code inside of the generated adapters. If you want to make your own adapter, read [here](../custom-objects/create_adapter_manually.md) and make sure you added the `build_runner` dependency to your `pubspec.yaml`!

Now we need to initialize Hive and the adapters in the `main()` function.

The `registerAdapter()` method is synchronous and it just takes an instance of the adapter. Read more [here](../custom-objects/type_adapters.md).

```dart
const String contactsBoxName = "contacts";

void main() async {
  await Hive.initFlutter();
  Hive.registerAdapter<Contact>(ContactAdapter());
  Hive.registerAdapter<Relationship>(RelationshipAdapter());
  await Hive.openBox<Contact>(contactsBoxName);
  runApp(MyApp());
}
```

!> As of Hive 1.3.0, the `registerAdapter()` method no longer takes a `typeId` parameter. The `@HiveType` annotation has a paramter for `typeId` now.

## Main App Structure

All of the UI code (except for the form) is in one widget, `MyApp`. `MyApp` contains a `MaterialApp` with a `ValueListenableBuilder` which listens to our box that we opened earlier and rebuilds the UI when it changes.

So there's a lot going on here! Let's break it down.

Inside of the `ValueListenableBuilder`, we check if the box is empty and return a `Text` widget notifying the user that they don't have any contacts stored in the app.

However, if the box is not empty, we need to show the user their stored contacts so for that we use a `ListView.builder`.

Using the index provided by the list builder, we can get the contact and access it's information to display it however we want. We're also making use of that `Map` to convert the `Relationship` enum (provided by the contact) to a displayable string.

Notice how I put a `InkWell` widget above the `Card`. We're going to use that so when the user does a long press on the card, it will show a dialog asking the user if they would like to delete the contact. For now it's empty but we'll get back to it later.

We're also using a `FloatingActionButton` (FAB) to navigate the user to the `AddContact` screen.

This is a very simple layout and I challenge you to improve upon it and make the app look gorgeous!

!> Since we're not storing the keys ourselves, we're using `Box.getAt()` instead of `Box.get()`.

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Contacts App',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Contacts App with Hive'),
        ),
        body: ValueListenableBuilder(
          valueListenable: Hive.box<Contact>(contactsBoxName).listenable(),
          builder: (context, Box<Contact> box, _) {
            if (box.values.isEmpty)
              return Center(
                child: Text("No contacts"),
              );
            return ListView.builder(
              itemCount: box.values.length,
              itemBuilder: (context, index) {
                Contact currentContact = box.getAt(index);
                String relationship =
                    relationshipString[currentContact.relationship];
                return Card(
                  clipBehavior: Clip.antiAlias, 
                  child: InkWell(
                    onLongPress: () { /* ... */ },
                    child: Padding(
                      padding: const EdgeInsets.all(8.0),
                      child: Column(
                        crossAxisAlignment: CrossAxisAlignment.start,
                        children: <Widget>[
                          SizedBox(height: 5),
                          Text(currentContact.name),
                          SizedBox(height: 5),
                          Text(currentContact.phoneNumber),
                          SizedBox(height: 5),
                          Text("Age: ${currentContact.age}"),
                          SizedBox(height: 5),
                          Text("Relationship: $relationship"),
                          SizedBox(height: 5),
                        ],
                      ),
                    ),
                  ),
                );
              },
            );
          },
        ),
        floatingActionButton: Builder(
          builder: (context) {
            return FloatingActionButton(
              child: Icon(Icons.add),
              onPressed: () {
                Navigator.of(context).push(
                    MaterialPageRoute(builder: (context) => AddContact()));
              },
            );
          },
        ),
      ),
    );
  }
}
```

!> If you're copying the code, the `Builder` widget is required to make `Navigator.of(context)` work so don't remove it!

## Creating the form

The user needs to be able to create a contact so let's build a form for them to use.

This is a very simple form that does _not_ contain any validation whatsoever.

The fields we have are:

- Contact Name
- Contact Age
- Contact Phone
- Contact Relationship

Feel free to add as many fields as you like!

?> Note that we have an undefined method `onFormSubmit()`. We will take care of that in the next part.

?> Also note the `formKey` variable. This is used for form validation. For more information, click [here](https://flutter.dev/docs/cookbook/forms/validation).

```dart
class AddContact extends StatefulWidget {
  final formKey = GlobalKey<FormState>();

  @override
  _AddContactState createState() => _AddContactState();
}

class _AddContactState extends State<AddContact> {
  String name;
  int age;
  String phoneNumber;
  Relationship relationship;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: SingleChildScrollView(
          child: Form(
            key: widget.formKey,
            child: Padding(
              padding: const EdgeInsets.all(8.0),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: <Widget>[
                  TextFormField(
                    autofocus: true,
                    initialValue: "",
                    decoration: const InputDecoration(
                      labelText: "Name",
                    ),
                    onChanged: (value) {
                      setState(() {
                        name = value;
                      });
                    },
                  ),
                  TextFormField(
                    keyboardType: TextInputType.number,
                    initialValue: "",
                    maxLength: 3,
                    maxLengthEnforced: true,
                    decoration: const InputDecoration(
                      labelText: "Age",
                    ),
                    onChanged: (value) {
                      setState(() {
                        age = int.parse(value);
                      });
                    },
                  ),
                  TextFormField(
                    keyboardType: TextInputType.phone,
                    initialValue: "",
                    decoration: const InputDecoration(
                      labelText: "Phone",
                    ),
                    onChanged: (value) {
                      setState(() {
                        phoneNumber = value;
                      });
                    },
                  ),
                  DropdownButton<Relationship>(
                    items: relationshipString.keys.map((Relationship value) {
                      return DropdownMenuItem<Relationship>(
                        value: value,
                        child: Text(relationshipString[value]),
                      );
                    }).toList(),
                    value: relationship,
                    hint: Text("Relationship"),
                    onChanged: (value) {
                      setState(() {
                        relationship = value;
                      });
                    },
                  ),
                  OutlineButton(
                    child: Text("Submit"),
                    onPressed: onFormSubmit,
                  ),
                ],
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

### Submitting the form

We have a form and a submit button for it, great! But we can't do anything with it right now. Let's fix that.

I've added a `onFormSubmit()` method that will take care of the submit process and add data to our box.

!> After you add form validation, check to see if the form is valid before adding data to the box. Click [here](https://flutter.dev/docs/cookbook/forms/validation) for more information.

```dart
class _AddContactState extends State<AddContact> {
  // ...

  void onFormSubmit() {
    Box<Contact> contactsBox = Hive.box<Contact>(contactsBoxName);
    contactsBox.add(Contact(name, age, phoneNumber, relationship));
    Navigator.of(context).pop();
  }

  @override
  Widget build(BuildContext context) {
    // ...
  }
}
```

## Deleting a contact

Uh oh, you have too many contacts and now you have to delete some. How do we do that?

Remember that `InkWell` widget above the `Card`? We're going to use the `onLongPress` callback to open a dialog that asks the user whether or not they would like to delete the selected contact.

!> We're using `Box.deleteAt()` instead of `Box.delete()` because we're using auto-incrementing keys to store the contacts. Read more [here](../basics/auto_increment.md).

```dart
// inside of `InkWell` widget
onLongPress: () {
  showDialog(
    context: context,
    barrierDismissible: true,
    child: AlertDialog(
      content: Text(
        "Do you want to delete ${currentContact.name}?",
      ),
      actions: <Widget>[
        FlatButton(
          child: Text("No"),
          onPressed: () => Navigator.of(context).pop(),
        ),
        FlatButton(
          child: Text("Yes"),
          onPressed: () async {
            await box.deleteAt(index);
            Navigator.of(context).pop();
          },
        ),
      ],
    ),
  );
},
```

## The End

Congratulations, you have finished this tutorial where you have built a fully functional Contacts app. Feel free to change the UI to make it more beautiful than I did. Here's the full app source code: https://github.com/Reprevise/contacts_app_hive

```dart:flutter:500px
import 'package:flutter/material.dart';
import 'package:hive_flutter/hive_flutter.dart';
import 'package:hive/hive.dart';

const String contactsBoxName = "contacts";

@HiveType(typeId: 1)
enum Relationship {
  @HiveField(0)
  Family,
  @HiveField(1)
  Friend,
}
const relationshipString = <Relationship, String>{
  Relationship.Family: "Family",
  Relationship.Friend: "Friend",
};

@HiveType(typeId: 0)
class Contact {
  @HiveField(0)
  String name;
  @HiveField(1)
  int age;
  @HiveField(2)
  Relationship relationship;
  @HiveField(3)
  String phoneNumber;

  Contact(this.name, this.age, this.phoneNumber, this.relationship);
}

void main() async {
  await Hive.initFlutter();
  Hive.registerAdapter<Contact>(ContactAdapter());
  Hive.registerAdapter<Relationship>(RelationshipAdapter());
  await Hive.openBox<Contact>(contactsBoxName);
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Contacts App',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Contacts App with Hive'),
        ),
        body: ValueListenableBuilder(
          valueListenable: Hive.box<Contact>(contactsBoxName).listenable(),
          builder: (context, Box<Contact> box, _) {
            if (box.values.isEmpty)
              return Center(
                child: Text("No contacts"),
              );
            return ListView.builder(
              itemCount: box.values.length,
              itemBuilder: (context, index) {
                Contact currentContact = box.getAt(index);
                String relationship =
                    relationshipString[currentContact.relationship];
                return Card(
                  clipBehavior: Clip.antiAlias, 
                  child: InkWell(
                    onLongPress: () {
                      showDialog(
                        context: context,
                        barrierDismissible: true,
                        child: AlertDialog(
                          content: Text(
                            "Do you want to delete ${currentContact.name}?",
                          ),
                          actions: <Widget>[
                            FlatButton(
                              child: Text("No"),
                              onPressed: () => Navigator.of(context).pop(),
                            ),
                            FlatButton(
                              child: Text("Yes"),
                              onPressed: () async {
                                await box.deleteAt(index);
                                Navigator.of(context).pop();
                              },
                            ),
                          ],
                        ),
                      );
                    },
                    child: Padding(
                      padding: const EdgeInsets.all(8.0),
                      child: Column(
                        crossAxisAlignment: CrossAxisAlignment.start,
                        children: <Widget>[
                          SizedBox(height: 5),
                          Text(currentContact.name),
                          SizedBox(height: 5),
                          Text(currentContact.phoneNumber),
                          SizedBox(height: 5),
                          Text("Age: ${currentContact.age}"),
                          SizedBox(height: 5),
                          Text("Relationship: $relationship"),
                          SizedBox(height: 5),
                        ],
                      ),
                    ),
                  ),
                );
              },
            );
          },
        ),
        floatingActionButton: Builder(
          builder: (context) {
            return FloatingActionButton(
              child: Icon(Icons.add),
              onPressed: () {
                Navigator.of(context).push(
                    MaterialPageRoute(builder: (context) => AddContact()));
              },
            );
          },
        ),
      ),
    );
  }
}

class AddContact extends StatefulWidget {
  final formKey = GlobalKey<FormState>();

  @override
  _AddContactState createState() => _AddContactState();
}

class _AddContactState extends State<AddContact> {
  String name;
  int age;
  String phoneNumber;
  Relationship relationship;

  void onFormSubmit() {
    if (widget.formKey.currentState.validate()) {
      Box<Contact> contactsBox = Hive.box<Contact>(contactsBoxName);
      contactsBox.add(Contact(name, age, phoneNumber, relationship));
      Navigator.of(context).pop();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: SingleChildScrollView(
          child: Form(
            key: widget.formKey,
            child: Padding(
              padding: const EdgeInsets.all(8.0),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: <Widget>[
                  TextFormField(
                    autofocus: true,
                    initialValue: "",
                    decoration: const InputDecoration(
                      labelText: "Name",
                    ),
                    onChanged: (value) {
                      setState(() {
                        name = value;
                      });
                    },
                  ),
                  TextFormField(
                    keyboardType: TextInputType.number,
                    initialValue: "",
                    maxLength: 3,
                    maxLengthEnforced: true,
                    decoration: const InputDecoration(
                      labelText: "Age",
                    ),
                    onChanged: (value) {
                      setState(() {
                        age = int.parse(value);
                      });
                    },
                  ),
                  TextFormField(
                    keyboardType: TextInputType.phone,
                    initialValue: "",
                    decoration: const InputDecoration(
                      labelText: "Phone",
                    ),
                    onChanged: (value) {
                      setState(() {
                        phoneNumber = value;
                      });
                    },
                  ),
                  DropdownButton<Relationship>(
                    items: relationshipString.keys.map((Relationship value) {
                      return DropdownMenuItem<Relationship>(
                        value: value,
                        child: Text(relationshipString[value]),
                      );
                    }).toList(),
                    value: relationship,
                    hint: Text("Relationship"),
                    onChanged: (value) {
                      setState(() {
                        relationship = value;
                      });
                    },
                  ),
                  OutlineButton(
                    child: Text("Submit"),
                    onPressed: onFormSubmit,
                  ),
                ],
              ),
            ),
          ),
        ),
      ),
    );
  }
}

// GENERATED CODE - DO NOT MODIFY BY HAND

// **************************************************************************
// TypeAdapterGenerator
// **************************************************************************

class RelationshipAdapter extends TypeAdapter<Relationship> {
  @override
  final typeId = 1;

  @override
  Relationship read(BinaryReader reader) {
    switch (reader.readByte()) {
      case 0:
        return Relationship.Family;
      case 1:
        return Relationship.Friend;
      default:
        return null;
    }
  }

  @override
  void write(BinaryWriter writer, Relationship obj) {
    switch (obj) {
      case Relationship.Family:
        writer.writeByte(0);
        break;
      case Relationship.Friend:
        writer.writeByte(1);
        break;
    }
  }
}

class ContactAdapter extends TypeAdapter<Contact> {
  @override
  final typeId = 0;

  @override
  Contact read(BinaryReader reader) {
    var numOfFields = reader.readByte();
    var fields = <int, dynamic>{
      for (var i = 0; i < numOfFields; i++) reader.readByte(): reader.read(),
    };
    return Contact(
      fields[0] as String,
      fields[1] as int,
      fields[3] as String,
      fields[2] as Relationship,
    );
  }

  @override
  void write(BinaryWriter writer, Contact obj) {
    writer
      ..writeByte(4)
      ..writeByte(0)
      ..write(obj.name)
      ..writeByte(1)
      ..write(obj.age)
      ..writeByte(2)
      ..write(obj.relationship)
      ..writeByte(3)
      ..write(obj.phoneNumber);
  }
}
```
