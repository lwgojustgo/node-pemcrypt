# Pemcrypt

Install with `npm`.

```bash
$ npm i pemcrypt --save
```

Require. Show me some CommonJS/Modules love!

```js
var pemcrypt = require('pemcrypt');
```

# Purpose

The goal of `pemcrypt` is to allow you to commit sensible environment configuration values in an encrypted manner to source control. All you need to do then, is get the private key originally used to encrypt a file, and you're good to go.

Ideally, this would only be used for development configuration values. You would distribute a secure file with your module, and give the private key to decrypt the file to your contributors. The upside is that you won't need to give them a new private file every time a value needs to change, but rather just encrypt it again, and push the new secure file.

Make sure you add `*.pem`, and whatever the decrypted filename is, to your `.gitignore`. Commiting either of those would defeat the entire purpose of this module.

# #pemcrypt.generateKey

Generates a `.pem` file the first time around. You can save it wherever you want, but **don't ever commit it to source control**.

```js
pemcrypt.generateKey(pemfile);
```

This method also returns the pem key right away if you want it for some reason.

# #pemcrypt(options)

Creates a pemcrypt `store` object. This will be used to `encrypt` and `decrypt` our files. This function will look for a `.pem` file and load it immediately, throwing if one isn't found.

```js
var store = pemcrypt({
    pem: pemfile,   // same one used to generate the key
    cwd: __dirname  // defaults to process.cwd()
});
```

The options let us determine the store _extensions_, for both the private and secure file names. These are the default values:

```js
{
    "raw": ".json" // the extension for unsecured files,
    "secure": ".pemjson", // the extension for encrypted files
    "algorithm": "aes256" // algorithm used to encrypt and decrypt
}
```

# #store.encrypt(sourceStore, targetStore)

Encrypts a raw private file. This method will take a file path relative to `cwd`, _without the private extension_. If `targetStore` equals `true`, the results are dumped to an encrypted secure file next to the private one. You can also pick a different name, if you want to keep secure and unsecure data in different places.

This method is _synchronous_ and returns the encrypted data, too.

```js
var pemjson = store.encrypt('env/defaults');

console.log(pemjson); // garbage

store.encrypt('env/defaults', true); // persisted to disk @ env/defaults.pemjson
store.encrypt('env/defaults', 'secure/defaults'); // persisted to disk @ secure/defaults.pemjson
```

# #store.decrypt(sourceStore, targetStore)

Decrypts an encrypted secure file. This method will take a file path relative to `cwd`, _without the secure extension_. If `targetStore` equals `true`, the results are dumped to an encrypted secure file next to the private one. You can also pick a different name, if you want to keep secure and unsecure data in different places.

This method is _synchronous_ and returns the decrypted data, too.

```js
var json = store.decrypt('env/defaults');

console.log(json); // data!

store.decrypt('env/defaults', true); // persisted to disk @ env/defaults.json
store.decrypt('env/defaults', 'private/defaults'); // persisted to disk @ private/defaults.json
```
