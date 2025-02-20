# AWS Account Setup

### Steps I Followed
1. Created an AWS account.
2. Enabled **Multi-Factor Authentication (MFA)** for the root user using Microsoft Authenticator.
3. Created an **IAM user with Administrator Access** instead of using the root account.
4. Enabled **MFA for the IAM user** using Chrome Passkeys.
5. Configured the admin user to **reset its password on the next login** for added security.
6. Ensured that all access follows AWS security best practices.

### Trying Different MFA Types
I wanted to try different MFA types to understand how they work and compare their usability and security features. Through my research, I found the following key differences:
- **Microsoft Authenticator** (used for the root user) generates time-based one-time passwords (TOTP) that expire after a short duration. It requires manual input but is widely supported and works offline.
- **Chrome Passkeys** (used for the IAM user) utilize public-key cryptography for passwordless authentication. They are device-bound and provide a seamless login experience but require a compatible device and browser.

Both methods enhance security, but passkeys offer a more user-friendly experience, while authenticators provide more flexibility across different platforms.

### Other Security Practices While Setting Up an AWS Account
During my research, I came across several best practices that enhance AWS security:
- **Enabling AWS CloudTrail**
  - Helps track account activity and detect unauthorized access or configuration changes.
- **Using IAM Roles Instead of Long-Term Access Keys**
  - Reduces the risk of compromised credentials by granting temporary access instead.
- **Setting Up AWS Config**
  - Helps monitor and evaluate configurations for compliance with security policies.
- **Enforcing Strong Password Policies**
  - Prevents weak credentials from being used in the environment.

Implementing these additional security measures ensures a well-protected AWS account and minimizes potential vulnerabilities.


### Security Measures Implemented and Why
- **Enabled MFA for both root and admin users** 
  - Adds an extra layer of security to prevent unauthorized access.
- **Created an IAM user instead of using the root account**
  - Root account has unrestricted access, increasing security risks if used daily.
  - IAM user allows for controlled and auditable access.
- **Restricted root account usage**
  - Reserved for critical account-level tasks (billing, recovery, etc.).
  - Reduces the likelihood of misconfigurations or security breaches.
- **Forced password reset on first login for admin user**
  - Ensures a fresh and secure password is set by the user.
  - Helps mitigate risks from any initial default password exposure.
  
### Why Do We Need an Admin User When We Have Root Access?
- Root account is meant for **critical** actions, not everyday tasks.
- IAM users allow **fine-grained access control**, limiting permissions as necessary.
- Using an admin IAM user helps **reduce security risks and accidental misconfigurations**.
- Follows AWS best practices to keep cloud environments secure.


