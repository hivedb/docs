# Encrypted box

Sometimes it is necessary to store data securely on the disk. Hive supports AES-256 encryption out of the box \(literally\).

The only thing you need is a 256-bit \(32 bytes\) encryption key. Hive provides a helper function to generate a secure encryption key using the [Fortuna](https://en.wikipedia.org/wiki/Fortuna_%28PRNG%29) random number generator:

Just pass the key when you open a box:

```dart:dart:400px
import 'dart:typed_data';
import 'package:hive/hive.dart';

void main() async {
  var keyBox = await Hive.openBox('encryptionKeyBox');
  if (!keyBox.containsKey('key')) {
    var key = Hive.generateSecureKey();
    keyBox.put('key', key);
  }
  
  var key = keyBox.get('key') as Uint8List;
  print('Encryption key: $key');

  var encryptedBox = await Hive.openBox('vaultBox', encryptionKey: key);
  encryptedBox.put('secret', 'Hive is cool');
  print(encryptedBox.get('secret'));
}
```

!> The example above stores the encryption key in an unencrypted box. You should **NEVER** do that.

## Important:

* Only values are encrypted while keys are stored in plaintext.
* Make sure to store the encryption key securely when your application is closed. With Flutter you can use the [flutter\_secure\_storage](https://pub.dev/packages/flutter_secure_storage) or a similar package.
* There is no check if the encryption key is correct. If it isn't, there may be unexpected behavior.



