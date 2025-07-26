---
description: >-
  View our organizational security rules. Information Security Program, related
  roles and responsibilities, third-party audits we undergo, and other details.
---

# Security

### Organizational Security

#### **Information Security Program**

We have an Information Security Program in place that is communicated throughout the organization. Our Information Security Program follows the criteria set forth by the SOC2 Framework. SOC2 is a widely known information security auditing procedure created by the American Institute of Certified Public Accountants.

#### **Third-Party Audits**

Our organization undergoes independent third-party assessments to test our security and compliance controls.

#### **Third-Party Penetration Testing**

We perform independent third-party penetration testing at least annually to ensure that the security posture of our services is not compromised.

#### **Roles and Responsibilities**

Roles and responsibilities related to our Information Security Program and the protection of our customer’s data are well defined and documented. Our team members are required to review and accept all of the security policies.

#### **Security Awareness Training**

Our team members are required to go through employee security awareness training covering industry standard practices and information security topics such as phishing and password management.

#### **Confidentiality**

All team members are required to sign and adhere to an industry standard confidentiality agreement prior to their first day of work.

#### **Background Checks**

We perform background checks on all new team members in accordance with local laws.

### Cloud Security

#### **Cloud Infrastructure Security**

All of our services are hosted with Google Cloud Platform (GCP). They employ a robust security program with multiple certifications. For more information on our provider’s security processes, please visit GCP Security.

#### **Data Hosting Security**

All of our data is hosted on Google Cloud Platform (GCP) databases. These databases are all located in the \[United States]. Please reference the above vendor specific documentation linked above for more information.

**Encryption at Rest**

All databases are encrypted at rest.

#### **Encryption in Transit**

Our applications encrypt in transit with TLS/SSL only.

**Vulnerability Scanning**

We perform vulnerability scanning and actively monitor for threats.

**Logging and Monitoring**

We actively monitor and log various cloud services.

**Business Continuity and Disaster Recovery**

We use our data hosting provider’s backup services to reduce any risk of data loss in the event of a hardware failure. We utilize monitoring services to alert the team in the event of any failures affecting users.

**Incident Response**

We have a process for handling information security events which includes escalation procedures, rapid mitigation and communication.

### Access Security

**Permissions and Authentication**

Access to cloud infrastructure and other sensitive tools are limited to authorized employees who require it for their role.

Where available we have Single Sign-on (SSO), 2-factor authentication (2FA) and strong password policies to ensure access to cloud services are protected.

**Least Privilege Access Control**

We follow the principle of least privilege with respect to identity and access management.

**Quarterly Access Reviews**

We perform quarterly access reviews of all team members with access to sensitive systems.

**Password Requirements**

All team members are required to adhere to a minimum set of password requirements and complexity for access.

**Password Managers**

All company issued laptops utilize a password manager for team members to manage passwords and maintain password complexity.

### Vendor and Risk Management

**Annual Risk Assessments**

We undergo annual risk assessments to identify any potential threats, including considerations for fraud.

**Vendor Risk Management**

Vendor risk is determined and the appropriate vendor reviews are performed prior to authorizing a new vendor.

### Contact Us

If you have any questions, comments or concerns or if you wish to report a potential security issue, or if you are looking for our SOC2 report, please contact [<mark style="color:blue;">security@aviator.co</mark>](mailto:security@aviator.co).



### How to report an issue

If you have discovered an issue that is not part of our out-of-scope vulnerabilities, please send an email to security@aviator.co with the following details:

* A summary of the issue and its potential impact
* A detailed breakdown of the steps to replicate the issue
* Details of the environment you are using (e.g., operating system, browser version)
* If available, any proof-of-concept code to demonstrate exploiting the vulnerability

#### What happens next

Upon receiving your report our security team will investigate the issue. We'll keep you updated on the progress and reach back out to you if we need additional information.

We value the time and effort put into responsible disclosure. For valid vulnerabilities considered in-scope with a CVSS score of 4 or higher, may be eligible for a financial reward. The specific amount will be determined based on the severity and impact of the issue.

#### In scope

* https://aviator.co
* https://app.aviator.com
* https://api.aviator.com

#### Out-of-scope

* Automated scanning of any kind
* Social engineering attempts, especially targeting Aviator employees
* Denial of Service (DoS) attacks of any kind
* Attacks requiring physical access to the victim's device
* Theoretical attacks without proof of exploitability
* Man-in-the-middle attacks
* Clickjacking on pages with no sensitive actions
* High-privilege users (admins, owners) using a bug to sabotage/deface their own workspace
* Logic bugs which allow an attacker to bypass limits on free account or get access to features on paid plans
* Missing best practices in Content Security Policy (CSP), email DNS records, or cookies (these may be considered informative but are unlikely to qualify for rewards)

#### Additional requests

To ensure a safe and responsible vulnerability research process, we kindly ask that you adhere to the following guidelines:

* **Test on your own account:** Please only test vulnerabilities using your own account or with explicit permission from the account holder. This helps protect our users' privacy and data integrity.
* **Respect privacy and data:** We ask that you make every privacy violations, unauthorized access to data, and any actions that could potentially damage or destroy data.
* **Maintain service integrity:** Please make a good faith effort to avoid interruption or degradation of our services. interruption or degradation of our services.
* **Limited scope:** If you manage to gain remote access to our systems, please do not attempt to expand or elevate your access to other servers.
* **Responsible disclosure:** To prevent potential exploitation, we kindly request that you do not make the vulnerability public before reporting it to us, and give us adequate time to address the issue.
