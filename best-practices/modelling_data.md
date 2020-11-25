# Modelling data

Hive does not have a query language and only has limited support for sorting, which is not necessarily a disadvantage, as you can see in the benchmark. Sorting and filtering are much faster if you do it yourself in Dart.

Since key-value stores are really simple, data can be modeled in a less complicated manner than, for example, in SQLite. There is no schema, and you can store objects of different types in the same box. But there are a few things you should keep in mind.

## Key order

All keys are sorted in lexicographic order by default. You can use that to get "free" ordering. For example, if you want to store users, you could use the last names and a unique number as a key to sort them by their last name.

You can also provide a custom key sorting function. For example, you could sort users in reverse lexicographic order.

## Lists vs Auto Increment

If you want to store a list of items, you have two options. Either store the list directly as a value \(using `put('myKey', [1, 2, 3])`\) or you can store each item individually \(using `add(1)`, `add(2)` etc.\).

If you want to store a big list which is updated frequently \(for example, a list of messages\), you should store the items individually. Otherwise, the whole list has to be written each time you change an item.

## Filtering items

You can easily filter items in your box. For example:

```dart
var filteredUsers = userBox.values.where((user) => user.name.startsWith('s'));
```

It might be a good idea to cache the result to improve performance.

