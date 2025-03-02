# [Hardcoded reCAPTCHA secret key] found in Odyssey CMS 10.35 - (odyssey_contact_form.php)

Affected Project: **Odyssey CMS 10.35**

Exploit Author: Dimitris Chatzidimitris

Platform: Web/Php

Vendor: Odyssey

Official Application Link: (https://odyssey.webpage.gr/new/)

Version: 10.35

<br />


## Description:
The application embeds a hardcoded reCAPTCHA secret key `(6LesLCATAAAAADWqbi8Uro_z_OLzekDuxdAkdzX4)` within the source code file `modules/odyssey_contact_form/odyssey_contact_form.php`. 

<br /> 


## Root cause:
Vulnerability found at: **odyssey_contact_form.php**

Path: modules/odyssey_contact_form/odyssey_contact_form.php

<br />  


## Vulnerability Analysis:
Because the secret key is exposed, the attacker can bypass the reCAPTCHA validation by forging requests or using the key to validate their own malicious requests.
The code checks if the g-recaptcha-response is present and validates it with Google's API, but it does not verify the hostname or action returned by Google's API. 

<br />


## Impact:
Reuse CAPTCHA response: This could allow an attacker to reuse a valid CAPTCHA response from another site or form.

Impersonate the Application: Use the key to generate valid reCAPTCHA responses for their own malicious site or service, potentially exhausting the legitimate site’s reCAPTCHA quota or incurring costs if tied to a paid tier.

Exploit Weak Form Handling: The CAPTCHA is the only defense against automated submissions and when the attacker bypasses it, they could exploit the form’s purpose (e.g., account creation, password reset, or data submission) to perform actions like:

1) Creating fake accounts.
2) Triggering denial-of-service (DoS) by overwhelming the server with requests.

<br />


## Vulnerable code POC:
![Screenshot 2025-03-02 144435](https://github.com/user-attachments/assets/8ec02951-4b01-4001-96e2-3d0690876217)

<br />


## Summary of Recommendations:
1) Move the reCAPTCHA secret key to a secure location (e.g., environment variable).

2) Validate the hostname and action fields in the Google API response.

3) Implement rate limiting to prevent brute-force attacks.

4) Add CSRF protection to prevent cross-site request forgery.

5) Sanitize and validate all user input to prevent injection attacks.

6) Enforce HTTPS for all reCAPTCHA API requests.

7) Upgrade to reCAPTCHA v3 for better security.

<br />

> One Click Can Change Everything: Be Secure.
