---
layout: post
title: "AES Encryption and Decryption in Flutter using the encrypt Package"
date: 2023-04-30 23:45:00 -0530
categories: Flutter Security
---

Encryption is a critical part of application security, and AES (Advanced Encryption Standard) is one of the most widely used symmetric encryption algorithms. In Flutter, the [`encrypt`](https://pub.dev/packages/encrypt) package gives you a straightforward API for encrypting and decrypting values in Dart.

This guide walks through AES modes, keys, IVs, padding, and a small Flutter-ready example you can adapt in your app.

## AES Modes

The `encrypt` package supports several AES modes:

- `ECB` - Electronic Code Book
- `CBC` - Cipher Block Chaining
- `CFB` - Cipher Feedback
- `OFB` - Output Feedback
- `CTR` - Counter

Each mode has different security and operational tradeoffs. For most app data, avoid `ECB` because it can leak patterns in repeated plaintext blocks. Prefer a mode that uses a fresh IV or nonce for each encryption operation, such as `CBC` or `CTR`, depending on your protocol and backend requirements.

## Secret Key and IV

AES needs a secret key. Some modes also need an initialization vector (IV), which helps prevent repeated plaintext from producing repeated ciphertext.

- Keep the secret key private and never hard-code production keys in the app binary.
- Generate a fresh IV when the selected AES mode requires one.
- Store or transmit the IV alongside the ciphertext when needed. The IV is not secret, but it must match during decryption.

## Padding

Padding adds bytes to plaintext so it matches the AES block size. The `encrypt` package supports padding options such as:

- `PKCS7`
- `ISO10126`
- `ANSI X.923`

Your padding choice must be consistent between encryption and decryption.

## Flutter AES Example

The example below shows the basic flow with the `encrypt` package. It creates a 32-byte key, a 16-byte IV, encrypts a string, prints the Base64 ciphertext, and then decrypts it.

> For production apps, generate and manage keys through a secure key management flow. This sample is intentionally small so the API shape is easy to see.

```dart
import 'package:encrypt/encrypt.dart' as encrypt;

void main() {
  final key = encrypt.Key.fromLength(32);
  final iv = encrypt.IV.fromLength(16);
  final encrypter = encrypt.Encrypter(encrypt.AES(key));

  const plainText = 'some random text';

  final encrypted = encrypter.encrypt(plainText, iv: iv);
  print(encrypted.base64);

  final decrypted = encrypter.decrypt(encrypted, iv: iv);
  print(decrypted);
}
```

## Optional IV Field

The IV field is optional for `ECB` because that mode does not use an IV. Even so, if you switch modes later, keeping the encryption call shape consistent can reduce mistakes. The more important production guidance is to choose a mode appropriate for your threat model and avoid `ECB` for sensitive data.

## Conclusion

The `encrypt` package is a convenient way to add AES encryption and decryption to Flutter projects. Pick the AES mode deliberately, protect your key, use IVs correctly, and keep encryption and decryption settings consistent.

## FAQs

### What is AES encryption?

AES is a symmetric encryption algorithm used to secure data in transit or at rest. It is widely used because it is efficient and well studied.

### What is the `encrypt` package in Flutter?

`encrypt` is a Dart package that provides APIs for encryption and decryption, including AES support.

### What AES modes does the `encrypt` package support?

It supports modes including `ECB`, `CBC`, `CFB`, `OFB`, and `CTR`.

### What is a secret key?

A secret key is the private value used to encrypt and decrypt data. Anyone with the key can decrypt the ciphertext, so it must be protected.

### What is an initialization vector?

An initialization vector, or IV, is a value used by many AES modes to make encryption output less predictable. It usually does not need to be secret, but it must be available for decryption.

### What is padding?

Padding adds extra bytes to plaintext so it fits AES block-size requirements. Common padding options include `PKCS7`, `ISO10126`, and `ANSI X.923`.
