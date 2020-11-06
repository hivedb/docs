# Encrypted box

Sometimes it is necessary to store data securely on the disk. Hive supports AES-256 encryption out of the box \(literally\).

The only thing you need is a 256-bit \(32 bytes\) encryption key. Hive provides a helper function to generate a secure encryption key using the [Fortuna](https://en.wikipedia.org/wiki/Fortuna_%28PRNG%29) random number generator.

Just pass the key when you open a box:

```dart:dart:400px
import 'dart:convert';
import 'package:hive/hive.dart';
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

void main() async {
  final FlutterSecureStorage secureStorage = const FlutterSecureStorage();
  var containsEncryptionKey = await secureStorage.containsKey(key: 'key');
  if (!containsEncryptionKey) {
    var key = Hive.generateSecureKey();
    await secureStorage.write(key: 'key', value: base64UrlEncode(key));
  }
  
  var encryptionKey = base64Url.decode(await secureStorage.read(key: 'key'));
  print('Encryption key: $encryptionKey');

  var encryptedBox = await Hive.openBox('vaultBox', encryptionCipher: HiveAesCipher(encryptionKey));
  encryptedBox.put('secret', 'Hive is cool');
  print(encryptedBox.get('secret'));
}
```


!> The example above stores the encryption key using the [flutter\_secure\_storage](https://pub.dev/packages/flutter_secure_storage) package, but you can use any package/method you prefer for securely storing the encryption key when your application is closed.


## Important:
* Only values are encrypted while keys are stored in plaintext.
* There is no check if the encryption key is correct. If it isn't, there may be unexpected behavior.



