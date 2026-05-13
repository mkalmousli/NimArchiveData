![nimPGP Logo](https://publiccdn.albassort.com/nimPGP/images/logo.png)

# NIMPGP
read the docs here: https://publiccdn.albassort.com/nimPGP/releases/latest/docs/nimPGP.html

---
# What is nimPGP

nimPGP is a pgp utility which uses **Sequoia PGP** on the backend. It allows for simple and idiomatic ways to utilize pgp in your code.
It supports both parsing and generation of the following asymmetric algorithms on the primary keys and the subkeys in infinite configurations

```
Cv25519
RSA2k, RSA3k, RSA4k
P256, P384, P521
```

It is notable that it does NOT support any other algorithm. The following are somewhat common but not parsable:

```
RSA1K, RSA8K, ECC (and more)
```


# What nimPGP is not

nimPGP is NOT a Sequoia wrapper. It is not intended to replace the already existing Sequoia wrapper for Nim.

All of the code is implemented in Rust and has bindings for Nim. You will not be able to interact with the entire breadth and scope
of Sequoia. This is okay for everyday use but is not fit for, say, security analysis. Things are abstracted.

# Features
(main feature black dot, white dot is options / parameters)
- Certificate creation
    - User ids 
    - Expiration length
    - Primary Key Encryption algorithms
- Subkeys
    - Individual Expiration times independent from the Primary Key
    - Customizable keyflags for each key
    - Individual Encryption algorithms independent from the Primary Key
- Cert Encryption
    - Supports atomic passwords for each key as well as one single password for the entire Certificate
- Message Signing 
- Signature Verification
- Encrypting a message for a given Public Key
- Decrypting a message with a Private Key
    - Supports multiple private keys (keyrings) and password keyrings
- Revoking a subkey
- Revoking a Primary Key
- Getting the intended recipients (by keyid) of an encrypted message
- Scaffolding a key to a Cert object which contains all of its information

This covers most usecases, but there is more that can be covered in the future.

# Exception Hanndling
　The base exception is ```SequoiaException```, you can use this to catch all errors from nimPGG. 

The exceptions follow this inheritance pattern.

-   CertParsingException
    -   FailedToParseKeyPublicException
    -   FailedToParseKeyPrivateException
    -   FailedToParseKeyException
    -   CertIsInvalidException
-   MissMatchingKey
    -   ExpectedPrivateKeyGotPublicException
    -   IncorrectKeyFlagsException
-   PasswordException
    -   PasswordSetToAtomicButMissingEncryptedKeyidException
    -   IncorrectPasswordForSecretKeyException
    -   FoundEncryptedSecretButNoPasswordHandlerException
-   CertGenerationFailedException
    -   KeyCannotBeUsedForTransportAndSigningException
-   NimSideSequoiaException
    -   CertLacksPrivateKey
    -   MustBeLiveForMoreThan100SecondsException
-   CertMaybeRevokedException
-   CertRevokedException
-   FailedToWriteMessageException
-   NoKeyMeetsSelectionException
-   FailedToParseMessageException
-   PackedParserFailedToRecurseException
-   FailedToReadFromBufferException
-   FailedToRevokeSubkeyException
-   FailedToRevokePrimaryKeyException
-   FailedToEncryptKeyException
-   CertHasEncryptedKeyException

I've made all of the names very clear to what they are, even if they are long. This is to also help you understand which ones can come up in the functions you execute intuitively, in lieu of functioning helpers, as, on paper every function can raise any one of these.

For additional clarity, the docs specify what functions raise which exception. 

# Security Concerns

Any security issues affecting Sequoia affects you. These tools are not so different from the tools they provide that they are likely to pose a security risk.

---
# Memory

![nimPGP memory diagram](https://publiccdn.albassort.com/nimPGP/images/memory.png)
(This diagram shows how memory is read and written.)

On the backend, memory ownership and management operates on the following core principles. These principles prevents memory leaks, and simplify operations:
1. Pointers provided in parameters are NOT mutable. All data returned to Nim must transfer directly in the return value.
2. Where possible, avoid manual allocation of arrays from Rust. For example, keyids are transferred in a `;` separated string as they are hex values. 
3. All non-gc managed pointers must be `deallocated` prior to return of the procedure, before the result is given to the user. Be it `allocated` by Rust, or `allocated` by Nim.
4. Manual allocation of data can only occur in Rust if the function does not return an error. All `malloc` calls must happen directly before `return` is called
5. After the Rust function returns, before an exception can be raised from its `error_code`, all manually managed Nim pointers must be `deallocated`. This means that because of rule 4, no data leak will occur.
6. Thus, no manually allocated pointers will be exposed to the end user
![memory-flow](https://publiccdn.albassort.com/nimPGP/images/memory-flow.png)
(This diagram shows the order of operations to ensure memory safety.)

---
# Usage
```nim
import nimPGP
import times
#   Sets the keyflags
#   Note that, Sequoia does not allow for the creation of keys which both do Transport Encryption and Signing
let signingKey = newSubkey({ForSigning})
let transportKey = newSubkey({ForTransport, ForStorageEncryption})
let certUnencrypted = newCert(@[signingKey, transportKey], validLength = initDuration(days=365))
#Creates a handler used to encrypt a Cert
let pwHandler = newPasswordHandler("password12345!")
let certEncrypted = encryptKeys(certUnencrypted, pwHandler)
```
---
# Sounds useful! How do I install
Compile from source (recommended!):
```sh
#   This assumes this is an initial install
nimble install nimPGP 
#   Pay attention to the version installed, this assumes there is no prior versions
cd ~/.nimble/pkgs2/nimPGP*
#   Assuming you're on a debian derived-os
#   As of pre-release it runs: 
#   sudo apt install clang cargo llvm pkg-config nettle-dev -y
./scripts/install-deps-apt.sh
#   compiles and links the .so to /usr/lib and the headers to /usr/include
./scripts/link.sh
```
Download precompiled (linux-x86 only!)
```sh
sudo apt install clang cargo llvm pkg-config nettle-dev -y
#   Uses my private servers
sudo curl https://publiccdn.albassort.com/nimPGP/releases/latest/libnimPGP.so > /usr/lib/libnimPGP.so
sudo curl https://publiccdn.albassort.com/nimPGP/releases/latest/nimPGP.h > /usr/lib/nimPGP.h
nimble install nimPGP 
```
---
# Credits 
All code and documentation, and charts by Caroline Marceano as of initial release

Code Review, Consulting, and Inspiration: Leorize (Check out their stuff: https://github.com/nim-works/nimskull)

Code Review: Luyten Orion (Chronos) (Check out their stuff: https://github.com/Luyten-Orion)

Code Review by my personal friends and family.
---
We stand on the shoulders of giants
巨人の肩の上に立つ
Nani gigantum humeris
