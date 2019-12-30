```dart:flutter:800px
import 'package:flutter/material.dart';
import 'package:hive/hive.dart';
import 'package:hive_flutter/hive_flutter.dart';

void main() async {
  await Hive.openBox('counterBox');
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  final box = Hive.box('counterBox');

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: Text('Hive Counter')),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              Text('Refresh page to test persistence'),
              SizedBox(height: 10),
              Text('You have pushed the button this many times:'),
              WatchBoxBuilder(
                box: box,
                builder: (context, box) {
                  return Text(
                    box.get('counter', defaultValue: 0).toString(),
                    style: Theme.of(context).textTheme.display1,
                  );
                },
              )
            ],
          ),
        ),
        floatingActionButton: Row(
          mainAxisAlignment: MainAxisAlignment.end,
          children: <Widget>[
            FloatingActionButton(
              onPressed: () {
                var counter = box.get('counter', defaultValue: 0);
                box.put('counter', counter - 1);
              },
              tooltip: 'Decrement',
              child: Icon(Icons.remove),
            ),
            SizedBox(
              width: 8,
            ),
            FloatingActionButton(
              onPressed: () {
                var counter = box.get('counter', defaultValue: 0);
                box.put('counter', counter + 1);
              },
              tooltip: 'Increment',
              child: Icon(Icons.add),
            ),
          ],
        ),
      ),
    );
  }
}
```