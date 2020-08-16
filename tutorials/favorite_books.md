# Favorite Books Introduction

[![](https://img.shields.io/badge/author-%40Reprevise-blue)](https://github.com/Reprevise)

In this tutorial we will build a simple app which stores the user's favorite books. It features a list of popular books and data persistance all with Hive in under 100 lines of code!

## Source Code & Live Test

Here's the source: https://github.com/hivedb/samples/tree/master/favorite_books

Below you can find the final code and test the app.

(Reload to test persistance)

```dart:flutter:500px
import 'package:flutter/material.dart';
import 'package:hive/hive.dart';
import 'package:hive_flutter/hive_flutter.dart';

const favoritesBox = 'favorite_books';
const List<String> books = [
  'Harry Potter',
  'To Kill a Mockingbird',
  'The Hunger Games',
  'The Giver',
  'Brave New World',
  'Unwind',
  'World War Z',
  'The Lord of the Rings',
  'The Hobbit',
  'Moby Dick',
  'War and Peace',
  'Crime and Punishment',
  'The Adventures of Huckleberry Finn',
  'Catch-22',
  'The Sound and the Fury',
  'The Grapes of Wrath',
  'Heart of Darkness',
];

void main() async {
  await Hive.initFlutter();
  await Hive.openBox<String>(favoritesBox);
  runApp(MyApp());
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  Box<String> favoriteBooksBox;

  @override
  void initState() {
    super.initState();
    favoriteBooksBox = Hive.box(favoritesBox);
  }

  Widget getIcon(int index) {
    if (favoriteBooksBox.containsKey(index)) {
      return Icon(Icons.favorite, color: Colors.red);
    }
    return Icon(Icons.favorite_border);
  }

  void onFavoritePress(int index) {
    if (favoriteBooksBox.containsKey(index)) {
      favoriteBooksBox.delete(index);
      return;
    }
    favoriteBooksBox.put(index, books[index]);
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Favorite Books',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: Scaffold(
        appBar: AppBar(
          title: Text('Favorite Books'),
        ),
        body: ValueListenableBuilder(
          valueListenable: favoriteBooksBox.listenable(),
          builder: (context, Box<String> box, _) {
            return ListView.builder(
              itemCount: books.length,
              itemBuilder: (context, listIndex) {
                return ListTile(
                  title: Text(books[listIndex]),
                  trailing: IconButton(
                    icon: getIcon(listIndex),
                    onPressed: () => onFavoritePress(listIndex),
                  ),
                );
              },
            );
          },
        ),
      ),
    );
  }
}
```

## Setup

First we create a new Flutter project:

```
flutter create favorite_books
```

## Dependencies

We can then go ahead and add `hive` and `hive_flutter` to the `pubspec.yaml` file in the project folder:

```yaml
name: favorite_books

environment:
  sdk: '>=2.6.0 <3.0.0'

dependencies:
  flutter:
    sdk: flutter
  hive: ^1.2.0
  hive_flutter: ^0.3.0+1

flutter:
  uses-material-design: true
```

## Initialization

I've defined a `const` variable to hold our `Box` name. Inside the `main()` function, we initialize Hive and open up the box. We also call `runApp()` to allow Flutter to build our app.

```dart
import 'package:flutter/material.dart';
import 'package:hive/hive.dart';
import 'package:hive_flutter/hive_flutter.dart';

const favoritesBox = 'favorite_books';

void main() async {
  await Hive.initFlutter();
  await Hive.openBox<String>(favoritesBox);
  runApp(MyApp());
}
```

## Our Data

For this app, we get data from a list of strings but you can easily do the same with an external API!

Each of the items in our list has a secret number called an index that Dart auto assigns for us, remember this for later! The list's index starts counting at 0, not 1!

```dart
const List<String> books = [
  // book name, index
  'Harry Potter', // 0
  'To Kill a Mockingbird', // 1
  'The Hunger Games', // 2
  'The Giver', // 3
  'Brave New World', // 4
  'Unwind', // 5
  'World War Z', // 6
  'The Lord of the Rings', // etc...
  'The Hobbit',
  'Moby Dick',
  'War and Peace',
  'Crime and Punishment',
  'The Adventures of Huckleberry Finn',
  'Catch-22',
  'The Sound and the Fury',
  'The Grapes of Wrath',
  'Heart of Darkness',
];
```

## MyApp Widget

Here's the `MyApp` class that we call inside of `runApp()`. We have some undefined functions and variables but we'll take care of those later.

The `MyApp` widget has a `Scaffold` which has a `ValueListenableBuilder`. That will rebuild the widget when our box changes.

Inside that builder is a `ListView` that holds all of the books in a `ListTile`.

```dart
class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  // ...

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Favorite Books',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: Scaffold(
        appBar: AppBar(
          title: Text('Favorite Books'),
        ),
        body: ValueListenableBuilder(
          valueListenable: favoriteBooksBox.listenable(),
          builder: (context, Box<String> box, _) {
            return ListView.builder(
              itemCount: books.length,
              itemBuilder: (context, listIndex) {
                return ListTile(
                  title: Text(books[listIndex]),
                  trailing: IconButton(
                    icon: getIcon(listIndex),
                    onPressed: () => onFavoritePress(listIndex),
                  ),
                );
              },
            );
          },
        ),
      ),
    );
  }
}
```

## Getting the box

Before we can even do anything with the box, we have to get it. We already opened the box when we initialized Hive. The great thing about Hive is that you can get boxes anywhere, you don't have to pass the box down from widget to widget. Just call `Hive.box()`. It's a synchronous method so no messy `async await` stuff. It reads all of the values from the box and puts them in memory so we can access them.

```dart
class _MyAppState extends State<MyApp> {
  Box<String> favoriteBooksBox;

  @override
  void initState() {
    super.initState();
    favoriteBooksBox = Hive.box(favoritesBox);
  }
  // ...
}
```

## Writing to the Box

Inside of `onFavoritePressed`, we react to the favorite icon being pressed inside of the `ListTile` widget. Here, we're checking if our box already contains the book index and delete it if so because you can't favorite a book twice. If it doesn't contain the index, then it will be put inside the Hive box and the list will be rebuilt because we are listening to changes in the box using `ValueListenableBuilder`.

!> In this scenario, the `putAt` function won't work as our `favoriteBooksBox`'s index is different from our book list's index! The `putAt` function is useful for updating data if you know the index it's in inside the Hive box.

```dart
class _MyAppState extends State<MyApp> {
  // ...
  void onFavoritePress(int index) {
    if (favoriteBooksBox.containsKey(index)) {
      favoriteBooksBox.delete(index);
      return;
    }
    favoriteBooksBox.put(index, books[index]);
  }

  @override
  Widget build(BuildContext context) {
    // ...
  }
}
```

## Reading from the Box

So, we added items to the box but there's still one more issue! How does the user know if a book is already favorited?

We are going to change the icon and it's color if the book's index number is in the Hive box using the function below.

All the function does is return an `Icon` widget if our box contains the book index. Remember, the data stored in a Hive box is stored in a key-value store like a `Map`.

```dart
class _ListOfBooksState extends State<ListOfBooks> {
  // ...
  Widget getIcon(int index) {
    if (favoriteBooksBox.containsKey(index)) {
      return Icon(Icons.favorite, color: Colors.red);
    }
    return Icon(Icons.favorite_border);
  }
  // ...
}
```

## The End

Congratulations, you have finished this tutorial where you have built a fully functional app that saves your favorite books. Feel free to change the UI to make it more beautiful than I did and add more features like the ability to filter out those you did or didn't like.
