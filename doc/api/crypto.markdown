# Crypto

    Stability: 3 - Stable

Usa `require('crypto')` para acceder a este módulo.

El módulo crypto necesita que OpenSSL esté disponible en el sistema.
Ofrece una forma de encapsular credenciales seguras para ser usadas 
como parte de una red HTTPS segura o una conexión http.

Además ofrece un conjunto de envoltorios para los métodos hash, hmac, cipher, decipher, sign y verify de OpenSSL.

## crypto.createCredentials(details)

Crea un objeto credenciales, con los detalles opcionales en forma de diccionario con las siguientes claves:

* `key` : cadena que contiene la clave privada codificada en PEM.
* `passphrase` : A string of passphrase for the private key
* `cert` : cadena que contiene el certificado codificado en PEM.
* `ca` : cadena o lista de cadenas de certificados de confianza codificados en PEM.
* `crl` : Either a string or list of strings of PEM encoded CRLs (Certificate Revocation List)
* `ciphers`: A string describing the ciphers to use or exclude. Consult
  <http://www.openssl.org/docs/apps/ciphers.html#CIPHER_LIST_FORMAT> for details
  on the format.

Si no se han dado ningún elemento en `ca`, node.js usará la lista de CAs de confianza publicadas como dice en
<http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt>.


### crypto.createHash(algorithm)

Crea y devuelve un nuevo objeto hash, un hash criptográfico con el algoritmo 
dado que puede ser usado para generar el hash digests.

`algorithm` depende de los algoritmos disponibles en la versión de OpenSSL en el sistema.
Algunos ejemplos son `'sha1'`, `'md5'`, `'sha256'`, `'sha512'`, etc. 
En versiones recientes, `openssl list-message-digest-algorithms` mostrará los algoritmos digest disponibles.

Example: this program that takes the sha1 sum of a file

    var filename = process.argv[2];
    var crypto = require('crypto');
    var fs = require('fs');

    var shasum = crypto.createHash('sha1');

    var s = fs.ReadStream(filename);
    s.on('data', function(d) {
      shasum.update(d);
    });

    s.on('end', function() {
      var d = shasum.digest('hex');
      console.log(d + '  ' + filename);
    });

## Class: Hash

The class for creating hash digests of data.

Returned by `crypto.createHash`.

### hash.update(data)

Actualiza el contenido del hash con el `data` dado. the encoding of which is given
in `input_encoding` and can be `'utf8'`, `'ascii'` or `'binary'`.
Defaults to `'binary'`.
Esto puede ser invocado muchas veces con dato nuevo mientras estos van llegando.

### hash.digest([encoding])

Calcula el digest todos los datos que van al hash.
La codificación (`encoding`) puede ser `'hex'`, `'binary'` o `'base64'`.
Por omisíón es `'binary'`.

Note: `hash` object can not be used after `digest()` method been called.


### crypto.createHmac(algorithm, key)

Crea y devuelve un objeto hmac, un hmac criptográfico con el algoritmo y la clave dadas.

`algorithm` depende de los algoritmos disponibles en la versión de OpenSSL en el sistema -  ver createHash arriba.
`key` es la clave hmac a usar.

## Class: Hmac

Class for creating cryptographic hmac content.

Returned by `crypto.createHmac`.

### hmac.update(data)

Actualiza el contenido del hmac con el `data` dado.
Esto puede ser invocado muchas veces con dato nuevo mientras estos van llegando.

### hmac.digest(encoding='binary')

Calcula el digest (resumen) de todos los datos que van al hmac.
La codificación (`encoding`) puede ser `'hex'`, `'binary'` o `'base64'`.
Por omisíón es `'binary'`.

Note: `hmac` object can not be used after `digest()` method been called.


### crypto.createCipher(algorithm, key)

Crea y devuelve un objeto cipher (codificador), con el algoritmo y la clave dadas.

`algorithm` es dependiente de OpenSSL, por ejemplo `'aes192'`, etc.
En versiones recientes, `openssl list-cipher-algorithms` mostrará 
los algoritmos cipher disponibles.
`password` is used to derive key and IV, which must be `'binary'` encoded
string (See the [Buffer section](buffer.html) for more information).

## crypto.createCipheriv(algorithm, key, iv)

Creates and returns a cipher object, with the given algorithm, key and iv.

`algorithm` is the same as the `createCipher()`. `key` is a raw key used in
algorithm. `iv` is an Initialization vector. `key` and `iv` must be `'binary'`
encoded string (See the [Buffer section](buffer.html) for more information).

## Class: Cipher

Class for encrypting data.

Returned by `crypto.createCipher` and `crypto.createCipheriv`.

### cipher.update(data, [input_encoding], [output_encoding])

Actualiza el cipher con `data`, la codificación viene dada en 
`input_encoding` y puede ser `'utf8'`, `'ascii'` o `'binary'`. 
Por omisión `'binary'`. 

El `output_encoding` especifica el formato de la salida del dato codificado,
y puede ser `'binary'`, `'base64'` o `'hex'`. Por omisión `'binary'`.

Devuelve el contenido codificado, y puede ser llamado muchas veces a medida que nuevos datos van llegando.

### cipher.final([output_encoding])

Devuelve cualquier contenido codificado restante, donde `output_encoding` puede ser:
`'binary'`, `'base64'` o `'hex'`. Por omisión `'binary'`.

Note: `cipher` object can not be used after `final()` method been called.

### cipher.setAutoPadding(auto_padding=true)

You can disable automatic padding of the input data to block size. If `auto_padding` is false,
the length of the entire input data must be a multiple of the cipher's block size or `final` will fail.
Useful for non-standard padding, e.g. using `0x0` instead of PKCS padding. You must call this before `cipher.final`.


### crypto.createDecipher(algorithm, key)

Crea y devuelve un objeto decipher (decodificación), con el algoritmo y clave dado.
Este es el simétrico del objeto cipher (codificación) de arriba.

### decipher.update(data, input_encoding='binary', output_encoding='binary')

Actualiza el objeto decodificador con `data`, que puede estar codificado en `'binary'`, `'base64'` o `'hex'`.
El `output_decoding` especifica en qué formato devolver el texto plano decodificdo: `'binary'`, `'ascii'` o `'utf8'`.

## Class: Decipher

Class for decrypting data.

Returned by `crypto.createDecipher` and `crypto.createDecipheriv`.

### decipher.update(data, [input_encoding], [output_encoding])

Updates the decipher with `data`, which is encoded in `'binary'`, `'base64'`
or `'hex'`. Defaults to `'binary'`.

The `output_decoding` specifies in what format to return the deciphered
plaintext: `'binary'`, `'ascii'` or `'utf8'`. Defaults to `'binary'`.

### decipher.final([output_encoding])

Devuelve el texto plano decodificado restante, siendo
`output_encoding` `'binary'`, `'ascii'` o `'utf8'`.
Por omisión `'binary'`.

Note: `decipher` object can not be used after `final()` method been called.

### decipher.setAutoPadding(auto_padding=true)

You can disable auto padding if the data has been encrypted without standard block padding to prevent
`decipher.final` from checking and removing it. Can only work if the input data's length is a multiple of the
ciphers block size. You must call this before streaming data to `decipher.update`.

## crypto.createSign(algorithm)

Crea y devuelve un objeto firma (signing) con el algoritmo dado.
En versiones recientes, `openssl list-public-key-algorithms` muestra
los algoritmos de firmado disponibles. Por ejemplo: `'RSA-SHA256

## Class: Signer

Class for generating signatures.

Returned by `crypto.createSign`.

### signer.update(data)

Actualiza el objeto firma con los datos dados.
Puede ser llamado muchas veces a medida que nuevos datos van llegando.

### signer.sign(private_key, output_format='binary')

Calcula la firma en todos los datos actualizados pasados a través del objetvo firma.
`private_key` es una cadena que contiene la clave privada para firmar codificada en PEM.

Devuelve la firma en `output_format` que puede estar en `'binary'`, `'hex'` o 
`'base64'`. Por omisión `'binary'`.

Note: `signer` object can not be used after `sign()` method been called.

### crypto.createVerify(algorithm)

Crea y devuelve un objeto verificación con el algoritmo dado.
Este es el simétrico del objeto firma de arriba.

## Class: Verify

Class for verifying signatures.

Returned by `crypto.createVerify`.

### verifier.update(data)

Actualiza el objeto verificador con los datos dados.
Puede ser llamado muchas veces a medida que nuevos datos van llegando.

### verifier.verify(cert, signature, signature_format='binary')

Verifica los datos firmados usando `cert`, que es una cadena que contiene la llave pública codificada en PEM; y `signature`, que es la firma del dato previamente calculada; `signature_format` puede ser `'binary'`, `'hex'` o `'base64'`.

Devuelve true o false dependiendo en la validez de la firma para el dato y la clave pública dadas.

Note: `verifier` object can not be used after `verify()` method been called.

## crypto.createDiffieHellman(prime_length)

Creates a Diffie-Hellman key exchange object and generates a prime of the
given bit length. The generator used is `2`.

## crypto.createDiffieHellman(prime, [encoding])

Creates a Diffie-Hellman key exchange object using the supplied prime. The
generator used is `2`. Encoding can be `'binary'`, `'hex'`, or `'base64'`.
Defaults to `'binary'`.

## Class: DiffieHellman

The class for creating Diffie-Hellman key exchanges.

Returned by `crypto.createDiffieHellman`.

### diffieHellman.generateKeys([encoding])

Generates private and public Diffie-Hellman key values, and returns the
public key in the specified encoding. This key should be transferred to the
other party. Encoding can be `'binary'`, `'hex'`, or `'base64'`.
Defaults to `'binary'`.

### diffieHellman.computeSecret(other_public_key, [input_encoding], [output_encoding])

Computes the shared secret using `other_public_key` as the other party's
public key and returns the computed shared secret. Supplied key is
interpreted using specified `input_encoding`, and secret is encoded using
specified `output_encoding`. Encodings can be `'binary'`, `'hex'`, or
`'base64'`. The input encoding defaults to `'binary'`.
If no output encoding is given, the input encoding is used as output encoding.

### diffieHellman.getPrime([encoding])

Returns the Diffie-Hellman prime in the specified encoding, which can be
`'binary'`, `'hex'`, or `'base64'`. Defaults to `'binary'`.

### diffieHellman.getGenerator([encoding])

Returns the Diffie-Hellman prime in the specified encoding, which can be
`'binary'`, `'hex'`, or `'base64'`. Defaults to `'binary'`.

### diffieHellman.getPublicKey([encoding])

Returns the Diffie-Hellman public key in the specified encoding, which can
be `'binary'`, `'hex'`, or `'base64'`. Defaults to `'binary'`.

### diffieHellman.getPrivateKey([encoding])

Returns the Diffie-Hellman private key in the specified encoding, which can
be `'binary'`, `'hex'`, or `'base64'`. Defaults to `'binary'`.

### diffieHellman.setPublicKey(public_key, [encoding])

Sets the Diffie-Hellman public key. Key encoding can be `'binary'`, `'hex'`,
or `'base64'`. Defaults to `'binary'`.

### diffieHellman.setPrivateKey(public_key, [encoding])

Sets the Diffie-Hellman private key. Key encoding can be `'binary'`, `'hex'`,
or `'base64'`. Defaults to `'binary'`.

## crypto.getDiffieHellman(group_name)

Creates a predefined Diffie-Hellman key exchange object.
The supported groups are: `'modp1'`, `'modp2'`, `'modp5'`
(defined in [RFC 2412](http://www.rfc-editor.org/rfc/rfc2412.txt ))
and `'modp14'`, `'modp15'`, `'modp16'`, `'modp17'`, `'modp18'`
(defined in [RFC 3526](http://www.rfc-editor.org/rfc/rfc3526.txt )).
The returned object mimics the interface of objects created by
[crypto.createDiffieHellman()](#crypto.createDiffieHellman) above, but
will not allow to change the keys (with
[diffieHellman.setPublicKey()](#diffieHellman.setPublicKey) for example).
The advantage of using this routine is that the parties don't have to
generate nor exchange group modulus beforehand, saving both processor and
communication time.

Example (obtaining a shared secret):

    var crypto = require('crypto');
    var alice = crypto.getDiffieHellman('modp5');
    var bob = crypto.getDiffieHellman('modp5');

    alice.generateKeys();
    bob.generateKeys();

    var alice_secret = alice.computeSecret(bob.getPublicKey(), 'binary', 'hex');
    var bob_secret = bob.computeSecret(alice.getPublicKey(), 'binary', 'hex');

    /* alice_secret and bob_secret should be the same */
    console.log(alice_secret == bob_secret);

## crypto.pbkdf2(password, salt, iterations, keylen, callback)

Asynchronous PBKDF2 applies pseudorandom function HMAC-SHA1 to derive
a key of given length from the given password, salt and iterations.
The callback gets two arguments `(err, derivedKey)`.

## crypto.randomBytes(size, [callback])

Generates cryptographically strong pseudo-random data. Usage:

    // async
    crypto.randomBytes(256, function(ex, buf) {
      if (ex) throw ex;
      console.log('Have %d bytes of random data: %s', buf.length, buf);
    });

    // sync
    try {
      var buf = crypto.randomBytes(256);
      console.log('Have %d bytes of random data: %s', buf.length, buf);
    } catch (ex) {
      // handle error
    }