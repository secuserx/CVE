# [Hardcoded Keystore Password] found in FREENOW (ex Beat app) 12.10.0 - (SSL.java)

Affected Project: **FREENOW (ex Beat app)**

Platform: Android

Vendor: Intelligent Apps GmbH

Official Website: (https://play.google.com/store/apps/details?id=taxi.android.client)

Version: 12.10.0

Updated: Oct 29, 2024

<br />

> Installs: 10,000,000+

> Category: Tools

> Android Version Code: 2033 

> File Name: FREENOW - Taxi and more_12.10.0.apk

> Size: 89.83MB

> SHA256: bc9988e5d590f633aef4491ca7a190ab17678d086224c041a4f4f67c46b4a416

<br /> 


## Description:
The application embeds a well-known hardcoded `keystore` default password `changeit` within the source code file `ch/qos/logback/core/net/ssl/SSL.java`.

Because the key is hardcoded and packaged inside the APK, an attacker can extract it from the APK file. 
An attacker who gains access to the keystore file can easily unlock it using the known password `changeit`. 
This can lead to unauthorized access to sensitive cryptographic material such as private keys and certificates, which are crucial for establishing secure SSL/TLS connections.

<br /> 


## Root cause:

Vulnerability found at: **SSL.java**

Path: ch/qos/logback/core/net/ssl/SSL.java

<br />  


## Impact

When the attacker obtains the keystore protected by the default password `changeit`:

1. Extract Private Keys: They can extract private keys and certificates stored in the keystore.
2. Impersonate Servers: Use the extracted keys to impersonate the server in man-in-the-middle (MITM) attacks.
3. Sign Malicious Content: Sign malicious code or content to make it appear trustworthy.
4. Decrypt Traffic: Decrypt encrypted SSL/TLS traffic if they can intercept it.


Certificate/Key files hardcoded inside the app:

```
assets/ds-amex.pem
assets/ds-cartesbancaires.pem
assets/ds-discover.cer
assets/ds-mastercard.crt
assets/ds-visa.crt
```

This compromises the confidentiality and integrity of communications and can lead to broader security breaches within the system.

<br /> 


## Vulnerability Analysis:

Tool for decompiling the .apk file: [Jadx](https://github.com/skylot/jadx)

`SSL.java` contents: 

```
public interface SSL {
    public static final String DEFAULT_KEYSTORE_PASSWORD = "changeit";
    public static final String DEFAULT_KEYSTORE_TYPE = "JKS";
    public static final String DEFAULT_PROTOCOL = "SSL";
    public static final String DEFAULT_SECURE_RANDOM_ALGORITHM = "SHA1PRNG";
}
```

Within the code there is a hardcoded keystore password `changeit`:

```
public static final String DEFAULT_KEYSTORE_PASSWORD = "changeit";
```

The password `changeit` can be used to decrypt the keystore !

<br />


## Recommendations for Secure Keystore Key Management


**Using a Configuration File**

Create a properties file named `ssl-config.properties` with the following content:

```
keystore.password=YourStrongPasswordHere
keystore.type=JKS
protocol=TLS
secureRandomAlgorithm=SHA1PRNG
```

Make sure to:

Replace `YourStrongPasswordHere` with your actual new keystore password.
Secure this file by setting appropriate file permissions to restrict unauthorized access.

Then modify your application code to load the configuration from the properties file:

```
// Load properties from the configuration file
Properties properties = new Properties();
try (InputStream input = new FileInputStream("ssl-config.properties")) {
     properties.load(input);
}
```

<br />


## CWE & CVE score

CWE-312: Cleartext Storage of Sensitive Information
CVSS v3.1 Base Score: 7.8 (High)

<br />

> One Click Can Change Everything: Be Secure.
