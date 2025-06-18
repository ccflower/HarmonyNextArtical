# HarmonyOS Next enterprise-level data security "encrypted offensive and defensive battle"

Hello everyone, I am Xiao L, the female programmer who "carries an encryption gun" in the enterprise-level data security battlefield.Recently, I built a HarmonyOS Next data protection system for a company, which is like building a "quantum-level fortress" for data - today I will reveal the "offensive and defense strategy" and code arsenal, and by the way, how to "coexist peacefully" security and performance~


## 1. Corporate data: "Tang Monk's Meat" targeted
### (I) The "data vault" that must be held firmly
- **Business Confidential**: Core technical documents, undisclosed financial statements, customer resource statements - Every leak may cause competitors to "steal their homes".
- **Sensitive information**: Employee compensation data, user medical records, supply chain privacy - at a minority rate, at a serious rate, at a serious rate, at a social death.

### (II) Where may the enemy come from?
1. **Insider script**
- Employee A sent a private email to the customer list: "Anyway, I'm off work, I'll change the files at home" (I don't know that the email was targeted by phishing websites).
- Employee B uses public Wi-Fi to connect to the enterprise system: "Starbucks network speed is fast, so make do with a report" (the hacker is "stealing data from the Internet" at the next desk).
2. **Hack attack package**
- "Photo Email": Pretend that the boss sends "emergency documents", the attachment contains a Trojan horse, click "open the door to invite the thief".
- "Brute force cracking": Use dictionary attack to test passwords, trying to break open the door to the enterprise system.


## 2. Encryption architecture: Wear "double body armor" for data
### (I) "Combination punch" of storage + transmission
1. **Storage encryption: "underground vault" of data**
- Use the AES algorithm to "build a wall" for data:
Each byte of the order information in the enterprise database is encrypted with AES-256. Even if the hard disk is pried away, hackers can only see "Mars text".
*Analogy*: It’s like locking cash into a safe, hiding the key in the “secret passage” that only you know.
2. **Transmission encryption: "Armored armored car" of data**
- RSA is responsible for "deliverying keys", and AES is responsible for "moving goods":
When an employee connects the enterprise system with a mobile phone, he first exchanges the AES key with RSA (such as a courier handing over a temporary key), and then uses AES to encrypt and transmit data (use a temporary key to lock the box, and the courier cannot open it).

### (II) Key management: "Key Philosophy" which is more rigorous than spy movies
1. **Key generation: Reject "123456" style of distortion**
   ```typescript
// Generate AES-128 key (don't write it to death if you actually apply it! Use a random generator!)
async function to create AES key() {
const Key factory = cryptoFramework.createSymKeyGenerator('AES128');
// The real scene should call the randomly generated interface, which is only demonstrated here
const original key = new Uint8Array([83, 217, 231, 76, 28, 113, 23, 219, 250, 71, 209, 210, 205, 97, 32, 159]);
return await key factory.convertKey({ data: original key });
   }

// Generate RSA-1024 key pair (public key = door lock, private key = key)
async function creates RSA key pair () {
const algorithm = "RSA1024";
const factory = cryptoFramework.createAsyKeyGenerator(algorithm);
return await factory.generateKeyPair(); // output { priKey, pubKey }
   }
   ```
2. **Key distribution: Be as cautious as agent connector**
- Use SSL/TLS channel to transmit keys, prohibit the transmission of plain-text email or chat tools - prevent "insider intercepting" or "hackers are eavesdrop".
3. **Key storage: safer than bank vault**
- Private keys are stored in HarmonyOS keystore or hardware security module (HSM), and are prohibited from writing them in code - I have seen a project write a private key into annotation, and "dead" on the spot when it is audited.
4. **Key update: Regularly "change locks" to prevent prying doors**
- The symmetric key is changed once a quarter, and the RSA private key is changed every year - Imagine changing passwords for the safe regularly. The hacker just cracked the old password and found that the lock core had been replaced.


## 3. Code Practical Practice: "Eighteen Martial Arts" of Encryption Algorithm
### (I) AES: "Reinforced Cement" for Data Storage
1. **Encrypted storage: Fill the data with cement**
   ```typescript
   import { cryptoFramework } from '@kit.CryptoArchitectureKit';

async function seals data into cement tank (original data: string) {
const key = await AES key();
const encryption machine = cryptoFramework.createCipher('AES128|CBC|PKCS7');
const Random IV = new Uint8Array(16); // 16-byte random number, different every time you encrypt it
crypto.getRandomValues(RandomIV); // Use safe random number generator for real scenes
       
await encryption machine.init(cryptoFramework.CryptoMode.ENCRYPT_MODE, key, { iv: { data: random IV } });
const ciphertext = await crypto machine.doFinal({
data: new TextEncoder().encode(raw data)
       });
       
// Remember to save the IV and the ciphertext together when storing!IV is required for decryption
return { ciphertext: ciphertext.data, IV: random IV };
   }
   ```
*Key Details*: IV (initialization vector) must be random and unique, otherwise the same plaintext will encrypt the same ciphertext, exposing the data pattern.

2. **Decryption process: Use the key to remove cement**
   ```typescript
async function retrieves data from cement tank (ciphertext: Uint8Array, IV: Uint8Array, key: cryptoFramework.Key) {
const decryptor = cryptoFramework.createCipher('AES128|CBC|PKCS7');
await decryptor.init(cryptoFramework.CryptoMode.DECRYPT_MODE, key, { iv: { data: IV } });
const raw data Buffer = await decryptor.doFinal({ data: ciphertext });
return new TextDecoder().decode(raw data Buffer.data);
   }
   ```

### (II) RSA: "electronic seal" for identity authentication
1. **User login: First stamp the private key**
   ```typescript
// User side: Use the private key to "stamp" the password
async function: stamp a private seal on the password (private key: cryptoFramework.Key, password: string) {
const Signing Machine = cryptoFramework.createSign('RSA1024|PKCS1|SHA256');
await signing machine.init(private key);
await signing machine.update({ data: new TextEncoder().encode(password) });
return await signing machine.sign(null); // produce signature data
   }

// Server side: Use the public key to verify the "seal"
async function Verify the authenticity of the seal (public key: cryptoFramework.Key, password: string, signature: Uint8Array) {
const Chapter verification machine = cryptoFramework.createVerify('RSA1024|PKCS1|SHA256');
await checker.init(public key);
await checker.update({ data: new TextEncoder().encode(password) });
return await checking machine.verify(signature); // true=pass, false=forge
   }
   ```
*Principle*: Users use private keys to sign (equivalent to handwritten signatures), and servers use public keys to verify (equivalent to handwriting checking) to ensure that the password has not been tampered with by the intermediary.

2. **Data tamper-proof: paste "encrypted strip" to the file**
When transmitting financial statements, use the private key to sign the file hash value, and the receiver uses the public key to verify the signature - if the file is tampered with, the hash value cannot match, and the signature will naturally fail.
   ```typescript
async function seals the file (private key: cryptoFramework.Key, file content: string) {
const hash = await crypto.subtle.digest('SHA-256', new TextEncoder().encode(file content));
return await: stamp the password with a private seal (private key, new TextDecoder().decode(hash));
   }
   ```


## 4. The way to balance: "Dance for Duo" of safety and performance
### (I) Encryption strategy: "Michelin chef" who watches the dishes and eats
- **Top Secret Data** (such as core algorithm): AES-256 + GCM mode (provided with authentication encryption), sacrifice point performance for security.
- **Ordinary data** (such as internal notification): AES-128 + ECB mode (simple and fast), anyway, it will not be fatal if it is leaked.
- **Dynamic adjustment**: When the user is detected on the public network, double encryption will be automatically enabled; when the enterprise intranet, the encryption intensity will be appropriately reduced.

### (II) Performance optimization: "Press the accelerator" for encryption
1. **Segmented Encryption**:
When processing a 10GB database, encrypt it in 100MB to avoid memory explosions - it's like eating hot pot and rinsing it in a plate, don't put it all in the pot.
2. **Key Cache**:
Store commonly used keys into secure memory (such as trusted execution environment TEE) to avoid repeated loss of performance - but remember to add locks and don't let the cache become a "coupled".
3. **Hardware acceleration**:
Calling chip-level encryption instructions (such as ARM Cryptography Extensions) is 3-5 times faster than pure software encryption - equivalent to providing an "sports car" to the encryption algorithm.


## 5. Avoiding Pits: The "crypto traps" I've stepped on in those years
1. **Hardcoded key**:
Newbie often write keys into code comments or configuration files, and are scanned by hackers to "open the door and grab money" directly - the correct way: use environment variables or key management service (KMS) to obtain dynamically.
2. **Reuse IV**:
In order to save trouble, a project used fixed IV encryption, resulting in the same plaintext encryption results, and the data pattern was analyzed by hackers - IVs must be randomly generated each time!
3. **Over Encryption**:
A project encrypts the log files with AES-256, but the log writing is severely stuttered - remember: safety is a means, not an end, don't wear bulletproof helmets for mosquitoes.


## written at the end
Enterprise-level data security is like playing Go. Every step of encryption is a move, and you have to figure out the "offensive, defense, gains and losses".HarmonyOS Next's encryption toolchain is already mature, but the real moat is "suitable strategy + rigorous implementation".Next time I will talk to you about how to use Hongmeng’s distributed capabilities to create a “data zero trust architecture”. Remember to squat~

(Please indicate the source and original author Xiao L when reprinting. The thief will be "attacked" by my encryption algorithm~ 😉)
