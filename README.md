# OWASP Top 10 & Web Application Penetration Testing

<p align="center">
  <img src="https://img.shields.io/badge/Focus-Web%20Application%20Security-blue?style=for-the-badge" />
  <img src="https://img.shields.io/badge/OWASP-Top%2010-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Practice-PortSwigger-red?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Documentation-Markdown-black?style=for-the-badge" />
</p>

## About This Repository

This repository is a personal technical documentation and practical learning project focused on **Web Application Security and Penetration Testing**.

It documents my learning journey through common web application vulnerabilities, based on OWASP security concepts and hands-on exercises from PortSwigger Web Security Academy.

The repository combines theoretical knowledge with practical security testing, vulnerability analysis, exploitation techniques, and detailed lab solutions.

Each vulnerability directory contains two main documentation files:

- **Vulnerability Notes:** Technical explanations, vulnerability concepts, attack techniques, root causes, and mitigation methods.
- **PortSwigger Lab Solutions:** Practical lab walkthroughs, exploitation steps, payloads, HTTP request analysis, and key findings.

## Learning Objectives

- Understand common web application vulnerabilities.
- Study OWASP security concepts and testing methodologies.
- Develop practical web penetration testing skills.
- Analyze HTTP requests and responses using Burp Suite.
- Identify and exploit vulnerabilities in authorized environments.
- Understand authentication and authorization mechanisms.
- Explore REST API and GraphQL security.
- Document vulnerabilities, exploitation techniques, and mitigation methods.

## Web Security Topics

### 01. Broken Access Control
- Access Control Vulnerabilities
- IDOR
- Privilege Escalation
- Horizontal and Vertical Access Control
- Unauthorized Access

### 02. Cross-Site Request Forgery (CSRF)
- CSRF Fundamentals
- CSRF Tokens
- SameSite Cookies
- CSRF Protection Bypass

### 03. SQL Injection
- SQL Injection Fundamentals
- UNION-Based SQL Injection
- Blind SQL Injection
- Error-Based SQL Injection
- Authentication Bypass
- Filter Bypass

### 04. Cross-Site Scripting (XSS)
- Reflected XSS
- Stored XSS
- DOM-Based XSS
- Context-Based XSS
- XSS Filter Bypass

### 05. Server-Side Template Injection (SSTI)
- Template Engine Fundamentals
- Identifying SSTI
- Template Injection
- Server-Side Code Execution

### 06. Open Redirect
- URL Redirection
- Unvalidated Redirects
- Redirect Validation Bypass

### 07. XML External Entity (XXE)
- XML Fundamentals
- XXE Injection
- File Disclosure
- Blind XXE
- External Entity Processing

### 08. Authentication Vulnerabilities
- Username Enumeration
- Password Brute Force
- Authentication Bypass
- Password Reset Vulnerabilities
- Multi-Factor Authentication
- Session Management

### 09. Cross-Origin Resource Sharing (CORS)
- Same-Origin Policy
- CORS Configuration
- Origin Validation
- CORS Misconfiguration
- CORS Exploitation

### 10. Clickjacking
- Clickjacking Fundamentals
- UI Redressing
- Frame Protection
- X-Frame-Options
- CSP frame-ancestors

### 11. JSON with Padding (JSONP)
- JSONP Fundamentals
- Callback Parameters
- JSONP Security Risks
- Cross-Origin Data Exposure

### 12. LFI & Path Traversal
- Local File Inclusion
- Directory Traversal
- Path Manipulation
- Encoding Bypass
- File Access Restrictions

### 13. Host Header Attacks
- HTTP Host Header
- Password Reset Poisoning
- Host Header Injection
- Routing-Based Attacks

### 14. Business Logic Bugs
- Business Logic Vulnerabilities
- Workflow Bypass
- Input Validation Issues
- Race Conditions
- Logic Flaws

### 15. Unrestricted File Upload
- File Upload Validation
- Extension Bypass
- Content-Type Manipulation
- File Upload Restrictions
- Malicious File Upload Risks

### 16. Insecure Deserialization
- Serialization Fundamentals
- Deserialization Vulnerabilities
- PHP Object Injection
- Serialized PHP Sessions
- PHPGGC

### 17. Server-Side Request Forgery (SSRF)
- SSRF Fundamentals
- Internal Network Access
- URL Validation Bypass
- SSRF Filter Bypass
- Blind SSRF

### 18. APIs & JWT
- REST API Security
- API Endpoint Discovery
- API Authentication and Authorization
- GraphQL Security
- JWT Structure and Claims
- JWT Algorithm Confusion
- JWK Header Injection
- API Rate Limiting
- Mass Assignment
- Broken Object Level Authorization (BOLA)

## Documentation Structure

Each vulnerability topic is organized into a dedicated directory containing two main documentation files.

### 1. Vulnerability Notes

Contains theoretical and technical documentation, including:

- Vulnerability Overview
- How It Works
- Types and Attack Techniques
- Root Causes
- Detection Methods
- Security Impact
- Mitigation and Prevention

### 2. PortSwigger Lab Solutions

Contains practical lab walkthroughs and exploitation analysis, including:

- Lab Objectives
- Vulnerability Analysis
- Burp Suite Testing
- Exploitation Steps
- HTTP Requests and Responses
- Payloads
- Solution Explanation
- Key Takeaways

This structure combines theoretical knowledge with practical penetration testing experience.

## Practical Learning – PortSwigger Web Security Academy

Practical exercises are based on PortSwigger Web Security Academy.

The labs provide hands-on experience in identifying and exploiting web application vulnerabilities in controlled environments.

Each documented lab focuses on understanding the vulnerability, analyzing the attack scenario, testing exploitation techniques, and documenting the results.

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Burp Suite | Web Application Security Testing |
| PortSwigger Academy | Hands-on Security Labs |
| Nmap | Network Reconnaissance |
| Kali Linux | Penetration Testing Environment |
| Python | Scripting and Automation |
| Git & GitHub | Version Control and Documentation |

## Learning Methodology

1. Study the vulnerability and understand its root cause.
2. Explore attack vectors and exploitation techniques.
3. Analyze HTTP traffic using Burp Suite.
4. Perform security testing in authorized environments.
5. Practice exploitation through PortSwigger labs.
6. Document findings, payloads, and technical analysis.
7. Understand security impact and mitigation techniques.

## References & Learning Resources

- [OWASP Official Website](https://owasp.org/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [PortSwigger Burp Suite Documentation](https://portswigger.net/burp/documentation)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

## Disclaimer

This repository is intended for educational purposes and authorized security testing only.

All practical exercises are performed in controlled environments, including PortSwigger Web Security Academy labs.

No unauthorized systems or applications are targeted.

---

**Focus:** Web Application Security | Web Penetration Testing | OWASP | API Security

**Learn. Practice. Analyze. Document.**
