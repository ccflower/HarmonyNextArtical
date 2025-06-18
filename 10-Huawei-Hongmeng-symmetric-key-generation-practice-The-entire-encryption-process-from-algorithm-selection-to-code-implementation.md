# Huawei Hongmeng symmetric key generation practice: "The entire encryption process" from algorithm selection to code implementation

Hello everyone!I am Xiao L, the female programmer who "plays with keys" in the Hongmeng crypto world~ Last time we talked about the "trust magic" of distributed authentication, and today we will talk about symmetric key generation - this is a "foundation project" for data security.Whether it is storing passwords, passing files, or even Bluetooth, it is inseparable from it.Ready?Let’s talk about algorithm principles to actual code combat, which will lead you to open up the “encrypted Ren and Du meridians”~


## 1. Symmetric key: the "master key" for data security

### (I) Three major algorithms "Huashan Sword Controversy"
1. **AES: Young and promising "crypto responsibility"**
- Features: 128-bit grouping, 128/192/256-bit keys, fast speed and high security, it is Hongmeng's "default player"~
- Scenario: Mobile album encryption, online chat message encryption, processing big data without any pressure~
   ```plaintext
// AES256 encryption process
Plaintext → AES algorithm (256-bit key) → ciphertext
(Speed: Encrypting 1GB files takes only a few seconds, 3 times faster than 3DES!)
   ```

2. **3DES: A stable and reliable "transition veteran"**
- Features: Use 3 56-bit keys to do three DES encryption, the total length of the key is 168 bits, strong compatibility but slow speed~
- Scenario: Old system docking (such as bank legacy systems), or "spare tire" when the hardware does not support AES~
   ```plaintext
// 3DES encryption process
Plain text → DES encryption → DES decryption → DES encryption (3 keys) → ciphertext
(Advantages: Strong ability to resist differential attacks, disadvantages: processing speed is 1/5 of AES)
   ```

3. **SM4: The "domestic guard" with a strong root **
- Features: Domestic commercial cryptographic standards, all packets/keys are 128 bits, the encryption and decryption algorithm is the same, and the speed is comparable to AES~
- Scenario: "domestic encryption urgently needed" areas such as government and finance, such as government system data encryption~
   ```plaintext
// SM4 highlights
- Supports the second-level certification of the National Secretariat and meets the requirements of guarantee 2.0
- Under hardware acceleration, the encryption speed can reach more than 1000Mbps~
   ```

### (II) Key specification: Use string "taming" algorithm
Hongmeng uses a string of "algorithm name + key length" to specify the specifications, which is simple and straightforward:
- AES128: 128-bit AES key (16 bytes)
- 3DES192: 192-bit 3DES key (24 bytes)
- SM4_128: 128-bit SM4 key (16 bytes)
*Note*: The string must be strictly matched. If a letter is written incorrectly, the key generation will fail.


## 2. Randomly generate keys: Let the algorithm "create the keys by itself"

### (I) ArkTS: Asynchronously friendly "front-end solution"
```typescript
import { cryptoFramework } from '@kit.CryptoArchitectureKit';

async function generates AES256 key() {
// 1. Create a generator (specify AES256 specification)
const generator = cryptoFramework.createSymKeyGenerator('AES256');
    try {
// 2. Generate the key asynchronously (return to Promise)
const key = await generator.generateSymKey();
// 3. Extract binary data (convert to hexadecimal to facilitate viewing)
const key hexadecimal = new TextDecoder().decode(key.getEncoded().data);
console.log('generated AES256 key:', key hexadecimal);
return key;
} catch (Error) {
console.error('Key generation failed:', error.message);
throw error;
    }
}

// Use example
Generate AES256 key().then(key => {
// Encryption operation with a key...
});
```
* Advantages*:
- Use `async/await` to process asynchronously, and the code logic is clear
- Automatically handle key object lifecycle without manually freeing resources

### (II) C/C++: "Basic Solution" with Performance Priority
```c++
#include "CryptoArchitectureKit/crypto_sym_key.h"

OH_CryptoSymKey* Generates 3DES192 key() {
OH_CryptoSymKeyGenerator* generator = nullptr;
OH_CryptoSymKey* key = nullptr;
    
// 1. Create a generator (C language style, need to manually manage pointers)
if (OH_CryptoSymKeyGenerator_Create("3DES192", & Generator) != CRYPTO_SUCCESS) {
        return nullptr;
    }
    
// 2. Synchronously generate keys (suitable for scenarios with high real-time requirements)
if (OH_CryptoSymKeyGenerator_Generate(generator, & key) != CRYPTO_SUCCESS) {
OH_CryptoSymKeyGenerator_Destroy(generator); // Destroy in time when failure
        return nullptr;
    }
    
// 3. Destroy the generator (avoid memory leaks)
OH_CryptoSymKeyGenerator_Destroy(generator);
return key;
}

// Use example
OH_CryptoSymKey* Key = Generate 3DES192 key();
if (key) {
// Encryption operation with a key...
OH_CryptoSymKey_Destroy(key); // It must be destroyed after use!
}
```
*Precautions*:
- C/C++ needs to manually manage memory. Forgetting to call `Destroy` will cause memory leaks
- Synchronous calls may block threads, it is recommended to execute in child threads


## 3. Specify the data generation key: the correct posture of "custom key"

### (I) ArkTS: "silk conversion" from binary to key
```typescript
function binary to key (binary data: Uint8Array, algorithm specification: string) {
// 1. Encapsulate binary data as DataBlob
const data Blob = { data: binary data };
const generator = cryptoFramework.createSymKeyGenerator(algorithm specification);
    
    return new Promise((resolve, reject) => {
// 2. Asynchronous conversion (support callback processing results)
Generator.convertKey(data Blob, (Error, key) => {
if (Error) {
reject(Error);
                return;
            }
resolve(key);
        });
    });
}

// Example of usage: Convert 192-bit binary data to 3DES key
const custom binary = new Uint8Array([
    0x1a, 0x2b, 0x3c, 0x4d, 0x5e, 0x6f, 0x70, 0x81,
    0x92, 0xa3, 0xb4, 0xc5, 0xd6, 0xe7, 0xf8, 0x99,
0xaa, 0xbb, 0xcc, 0xdd, 0xee, 0xff // 24 bytes (192 bits)
]);

Binary to key (custom binary, '3DES192').then(key => {
console.log('Converted key algorithm:', key.algName); // Output: 3DES
});
```
*Applicable scenarios*:
- Get the pre-generated key from a hardware security module (such as SE chip)
- Read fixed keys in configuration files (be careful about key storage security!)

### (II) C/C++: "precise control" of the underlying interface
```c++
OH_CryptoSymKey* Data to SM4 key (const uint8_t* binary data, size_t data length) {
OH_CryptoSymKeyGenerator* generator = nullptr;
OH_CryptoSymKey* key = nullptr;
Crypto_DataBlob DataBlob = {
.data = binary data,
.len = data length
    };
    
// 1. Create an SM4 generator (the key length is fixed 128 bits, and the data must be 16 bytes)
if (OH_CryptoSymKeyGenerator_Create("SM4_128", & Generator) != CRYPTO_SUCCESS) {
        return nullptr;
    }
    
// 2. Synchronous conversion (must ensure that the data length meets the algorithm requirements)
if (OH_CryptoSymKeyGenerator_Convert(generator, &data Blob, &key) != CRYPTO_SUCCESS) {
OH_CryptoSymKeyGenerator_Destroy(generator);
        return nullptr;
    }
    
OH_CryptoSymKeyGenerator_Destroy(generator);
return key;
}

// Example of usage: 16-byte binary to SM4 key
uint8_t fixed key[] = {
    0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef,
    0xfe, 0xdc, 0xba, 0x98, 0x76, 0x54, 0x32, 0x10
}; // 16 bytes (128 bits)

OH_CryptoSymKey* sm4Key = Data to SM4 key (fixed key, sizeof (fixed key));
if (sm4Key) {
// Execute SM4 encryption...
    OH_CryptoSymKey_Destroy(sm4Key);
}
```
*Key Details*:
- The data length must strictly match the algorithm requirements (such as SM4 must be 16 bytes, 3DES must be 24 bytes)
- Binary data is recommended to be read from secure storage (such as Hongmeng's distributed keystore)


## 4. Security best practices: Don’t let the key run naked

### (I) Key storage: Find a "safe" for the key
1. **Hardware-level storage**
- Use Hongmeng's Trusted Execution Environment (TEE) to store keys, such as:
     ```typescript
     import { tee } from '@ohos.tee';
tee.storeKey('symmetricKey', key.getEncoded().data); // The key is stored in TEE to prevent software attacks
     ```

2. **Distributed Storage**
- Use a distributed keystore across device scenarios, automatically synchronize + encrypted transmission:
     ```typescript
     import { distributedKeyManager } from '@ohos.distributedKey';
distributedKeyManager.syncKey('device1', 'device2', key); // Secure synchronization to another device
     ```

### (II) Key life cycle management: "full monitoring" from birth to destruction
1. **Generation Phase**
- Avoid using weak keys (such as all 0s, continuous numbers), must be encrypted strong random number generator

2. **Using phase**
- The key is not recorded in the log plaintext
- Refresh the key immediately after sensitive operations (such as destroying the temporary key after the transfer is completed)

3. **Destruction Phase**
- Use a secure erase algorithm to destroy key data in memory to avoid residual
   ```c++
void securely destroy key (OH_CryptoSymKey* key) {
memset(key, 0, sizeof(OH_CryptoSymKey)); // Overwrite memory
OH_CryptoSymKey_Destroy(key);
   }
   ```

### (III) Algorithm Selection Guide
| Scenario | Recommended Algorithm | Reason |
|---------------------|----------------|-------------------------------|
| Mobile application data encryption | AES256 | Speed ​​and security balance, Hongmeng default support |
| National Security Compliance System | SM4 | Comply with national standards, government/financial essentials |
| Old system compatibility | 3DES192 | Strong compatibility, transitional use |
| Low-power devices (such as IoT) | AES128 | Lightweight, suitable for resource-constrained environments |


## 5. FAQs and Pit Avoidance Guide

### (I) "Key Mismatch" Error
- **Reason**: The algorithm specification does not match the key length (such as using AES192 string to generate a 16-byte key)
- **Solution**: Check string parameters (AES128=16 bytes, AES192=24 bytes, AES256=32 bytes)

### (II) "Memory Leak" Warning
- **Scenario**: Forgot to call the `Destroy` function in C/C++
- **Prevention**: Encapsulate key objects in RAII mode and automatically manage life cycles:
  ```c++
  class AutoSymKey {
  public:
      AutoSymKey(OH_CryptoSymKey* key = nullptr) : key_(key) {}
      ~AutoSymKey() { if (key_) OH_CryptoSymKey_Destroy(key_); }
      OH_CryptoSymKey* operator->() { return key_; }
  private:
      OH_CryptoSymKey* key_ = nullptr;
  };

// Use example
AutoSymKey key (generate 3DES192 key()); // Automatically destroy it if it leaves the scope
  ```

### (III) "Performance Bottleneck" Issues
- **Scenario**: Generate large keys synchronously in UI threads (such as AES256)
- **Optimization**: ArkTS uses `async/await`, C/C++ uses multi-threading to avoid blocking the main thread


## Last chat
Symmetric key generation is the first and most critical step of encryption - after all, if the "key" is not safe, no matter how thick the "lock" is, it is a decoration.Through standardized algorithm interfaces, secure storage mechanisms and convenient cross-language support, Hongmeng allows developers to easily build a reliable encryption system.Next time, let’s talk about “how to use Hongmeng’s asymmetric key to make digital signatures”, such as signing applications with tamper-remember to follow!😉

(Please indicate the source and original author Xiao L when reprinting. Violators... Let your key be randomly changed to one every day! Just kidding~)
