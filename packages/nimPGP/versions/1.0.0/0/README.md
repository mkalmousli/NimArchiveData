![nimPGP Logo](https://publiccdn.albassort.com/nimPGP/images/logo.png)

# NIMPGP
Read the docs here: https://publiccdn.albassort.com/nimPGP/releases/latest/docs/nimPGP.html

Self-Hosted git: https://gogs.albassort.com/albassort/nimPGP

Gitlab Git: https://gitlab.com/IAlbassort/nimPGP
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
# Versioning
Versions are split into 3 numbers separated by `.`, for example, 1.0.0.

The leftmost number represents the current feature-set, and increments when new functionality is added. 

The middle number represents bug-fixes and patches on the libnimPGP side, or major bugfixes/changes on the Nim side, usually resulting in incompatibility with the previous libnimPGP version.

The rightmost increments when minor changes/bugfixes, or updates to documentation are added in Nim, but not in libnimPGP. This is for compatibility, and to help prevent needless manual dependency updating.

1.0.1 is compatible with 1.0.0
1.0.29 is compatible with 1.0.0 through 1.0.28, etc
1.1.0 is not compatible with 1.0.1
2.0.0 is not compatible with 1.1.0
---

# Future
Currently, nothing is explicitly planned, save for expanding the API to cover more of Sequoia. 

Perhaps, if demand exists, I will make repositories to fix the manual dependency issue.

# Credits 
All code and documentation, and charts by Caroline Marceano as of initial release

Code Review, Consulting, and Inspiration: Leorize (Check out their stuff: https://github.com/nim-works/nimskull)

Code Review: Luyten Orion (Chronos) (Check out their stuff: https://github.com/Luyten-Orion)

The beautiful people in the Sequoia IRC, who helped me when I needed it most.

Code Review by my personal friends and family.

# My PGP-KEy
Use this to verify the signatures of pre-build binaries you download. The distribution folders have a file in it named signed-sum.asc, which contains a sha-512 of the binary provided. Verify its signature, to prevent MTM attacks.

```
-----BEGIN PGP PUBLIC KEY BLOCK-----
Comment: albassort <CarolineMarceano@albassort.com> 

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
=g3W9
-----END PGP PUBLIC KEY BLOCK-----
```

---
We stand on the shoulders of giants
巨人の肩の上に立つ
Nani gigantum humeris

Thank you for reading.

---
