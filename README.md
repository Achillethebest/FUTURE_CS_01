# Vulnerability Assessment Report: testphp.vulnweb.com

**Author:** Hodome Kokou Achille  
**Program:** Future Interns — Cyber Security Fellowship  
**Identification:** FIT/MAR26/CS6617  
**Date:** March 3, 2026  
**Target:** http://testphp.vulnweb.com

---

## 1. Introduction
This report documents a security assessment conducted as part of Task 1 for the Future Interns Cyber Security program. The objective was to evaluate the security posture of `testphp.vulnweb.com`, an application specifically designed for security testing. 

The assessment focused on identifying common web vulnerabilities, evaluating their potential impact, and providing actionable remediation steps based on industry best practices.

### Methodology and Toolset
To ensure a comprehensive analysis, a combination of automated scanning and manual inspection was used:
* **Nmap 7.95:** Utilized for network reconnaissance and service identification.
* **OWASP ZAP:** Employed for automated web application vulnerability scanning.
* **Browser Developer Tools:** Used for manual verification of HTTP headers and session management.

---

## 2. Reconnaissance and Infrastructure Analysis
The initial reconnaissance phase focused on identifying the underlying server infrastructure. A full service scan was performed using the following command:

`nmap -sV -sC -A -oN nmap_results.txt testphp.vulnweb.com`

**Key Findings:**
* **Host Environment:** The application is hosted on an AWS EC2 instance (IP: 44.228.249.3).
* **Encryption Status:** Only port 80 (HTTP) was found to be open. There is a complete absence of HTTPS/TLS encryption.
* **Server Software:** The application runs on Nginx 1.19.0, a version released in 2020 which is now considered outdated.

**Analysis:** The lack of encryption means that all data transmitted, including user credentials, is sent in cleartext. This creates a significant risk of interception by malicious actors on the same network.

---

## 3. Vulnerability Analysis and Risks

### High Risk Vulnerabilities
* **Cross-Site Scripting (XSS):** The application reflects user-controllable input directly into HTML attributes without adequate sanitization. An attacker could exploit this to execute malicious JavaScript in the context of a user's browser.
* **Absence of Anti-CSRF Protection:** State-changing forms do not implement unique anti-forgery tokens. This allows for Cross-Site Request Forgery (CSRF) attacks, where an authenticated user could be forced to perform unintended actions.

### Medium Risk Vulnerabilities
* **Missing Security Headers:** The server does not implement critical security headers such as `Content-Security-Policy` (CSP) and `X-Frame-Options`. This increases the success rate of injection attacks and leaves the application vulnerable to Clickjacking.
* **Outdated Infrastructure:** Running an older version of Nginx (1.19.0) exposes the system to known vulnerabilities that have been patched in more recent releases.

### Information Disclosure
* **Fingerprinting:** The server explicitly discloses its version and the backend language (PHP) via the `Server` and `X-Powered-By` headers. While not direct exploits, these details assist attackers during the reconnaissance phase.

---

## 4. Summary of Findings

| Vulnerability | Risk Level | Severity |
| :--- | :--- | :--- |
| Lack of HTTPS Encryption | High | Critical |
| Potential Reflected XSS | High | High |
| Missing Anti-CSRF Tokens | High | High |
| Missing CSP Header | Medium | Medium |
| Clickjacking Vulnerability | Medium | Medium |
| Outdated Nginx Version | Medium | Medium |
| Server Information Disclosure | Informational | Low |

**Total Findings:** 14 (3 High, 4 Medium, 7 Low/Informational)

---

## 5. Conclusion and Recommendations
The assessment indicates a high-risk security profile for the target application. The most critical vulnerabilities are related to the lack of transport layer security and improper input handling.

**Recommended Actions:**
1. **Enable TLS:** Secure all communications by implementing an SSL/TLS certificate.
2. **Input Validation:** Implement a strict input validation and context-aware output encoding framework to mitigate XSS risks.
3. **Hardening:** Configure the web server to suppress version headers and implement modern security headers (CSP, HSTS, X-Frame-Options).
4. **Patch Management:** Upgrade the Nginx web server to the latest stable version to address legacy vulnerabilities.

---
