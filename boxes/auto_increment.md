# Auto increment & indices

We already know that Hive supports unsigned integer keys. You can use auto-increment keys if you like. This is very useful for storing and accessing multiple objects. You can use a Box like a list.

```dart
var friends = Hive.box('friends');

friends.add('Lisa');            // index 0, key 0

friends.add('Dave');            // index 1, key 1

friends.put(123, 'Marco');      // index 2, key 123

print(friends.values);          // Lisa, Dave, Marco
```

There are also `getAt()`, `putAt()` and `deleteAt()` methods to access or change values by their index.

IIt is essential to understand the difference between `integer` keys and indices.

```dart
friends.putAt(2, 'Ben');
```

```dart
frinds.put(123, 'Ben');
```

Both of these operations do the same thing. They replace `Marco` with `Ben`. `putAt()` uses the index \(in this case `2`\), `put()` uses the key \(in this case `123`\).

By default, String keys are sorted lexicographically and they have also indices.

{% hint style="info" %}
Even if you only use auto increment keys, you should not rely on keys and indices being the same.
{% endhint %}

