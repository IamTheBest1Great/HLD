# Security — Consolidated HLD Notes

## Table of Contents

- [1. Security Fundamentals](#1-security-fundamentals)
  - [1.1 Why Security Matters in HLD](#11-why-security-matters-in-hld)
    - [1.1.1 Protecting User Data](#111-protecting-user-data)
    - [1.1.2 Preventing Unauthorized Access](#112-preventing-unauthorized-access)
    - [1.1.3 Ensuring System Integrity](#113-ensuring-system-integrity)
    - [1.1.4 Maintaining Availability](#114-maintaining-availability)
    - [1.1.5 Regulatory Compliance](#115-regulatory-compliance)
  - [1.2 Core Security Principles](#12-core-security-principles)
    - [1.2.1 Defense in Depth](#121-defense-in-depth)
    - [1.2.2 Least Privilege](#122-least-privilege)
    - [1.2.3 Zero Trust](#123-zero-trust)
    - [1.2.4 Secure by Default](#124-secure-by-default)
    - [1.2.5 Principle of Separation](#125-principle-of-separation)
    - [1.2.6 Fail Securely](#126-fail-securely)
  - [1.3 CIA Triad](#13-cia-triad)
    - [1.3.1 Confidentiality](#131-confidentiality)
    - [1.3.2 Integrity](#132-integrity)
    - [1.3.3 Availability](#133-availability)
- [2. Authentication & Authorization](#2-authentication--authorization)
  - [2.1 Authentication (AuthN)](#21-authentication-authn)
    - [2.1.1 Username & Password](#211-username--password)
    - [2.1.2 Multi-Factor Authentication (MFA)](#212-multi-factor-authentication-mfa)
    - [2.1.3 OAuth 2.0](#213-oauth-20)
    - [2.1.4 OpenID Connect (OIDC)](#214-openid-connect-oidc)
    - [2.1.5 Single Sign-On (SSO)](#215-single-sign-on-sso)
    - [2.1.6 Biometric Authentication](#216-biometric-authentication)
  - [2.2 Authorization (AuthZ)](#22-authorization-authz)
    - [2.2.1 Role-Based Access Control (RBAC)](#221-role-based-access-control-rbac)
    - [2.2.2 Attribute-Based Access Control (ABAC)](#222-attribute-based-access-control-abac)
    - [2.2.3 Fine-Grained Permissions](#223-fine-grained-permissions)
    - [2.2.4 Resource-Level Authorization](#224-resource-level-authorization)
  - [2.3 Session & Token Security](#23-session--token-security)
    - [2.3.1 JWT (JSON Web Tokens)](#231-jwt-json-web-tokens)
    - [2.3.2 Access Tokens](#232-access-tokens)
    - [2.3.3 Refresh Tokens](#233-refresh-tokens)
    - [2.3.4 Session Expiration](#234-session-expiration)
    - [2.3.5 Secure Cookie Handling](#235-secure-cookie-handling)
- [3. Encryption & Data Protection](#3-encryption--data-protection)
  - [3.1 Encryption Fundamentals](#31-encryption-fundamentals)
    - [3.1.1 Symmetric Encryption](#311-symmetric-encryption)
    - [3.1.2 Asymmetric Encryption](#312-asymmetric-encryption)
    - [3.1.3 Hashing](#313-hashing)
    - [3.1.4 Digital Signatures](#314-digital-signatures)
  - [3.2 Encryption at Rest](#32-encryption-at-rest)
    - [3.2.1 Disk Encryption](#321-disk-encryption)
    - [3.2.2 Database Encryption](#322-database-encryption)
    - [3.2.3 Object Storage Encryption](#323-object-storage-encryption)
    - [3.2.4 Key Management](#324-key-management)
  - [3.3 Encryption in Transit](#33-encryption-in-transit)
    - [3.3.1 TLS / HTTPS](#331-tls--https)
    - [3.3.2 TLS Handshake](#332-tls-handshake)
    - [3.3.3 Certificate Management](#333-certificate-management)
    - [3.3.4 Mutual TLS (mTLS)](#334-mutual-tls-mtls)
  - [3.4 Data Protection Techniques](#34-data-protection-techniques)
    - [3.4.1 Data Masking](#341-data-masking)
    - [3.4.2 Tokenization](#342-tokenization)
    - [3.4.3 PII Protection](#343-pii-protection)
    - [3.4.4 Field-Level Encryption](#344-field-level-encryption)
- [4. Application Security](#4-application-security)
  - [4.1 Input Validation & Output Encoding](#41-input-validation--output-encoding)
    - [4.1.1 SQL Injection Prevention](#411-sql-injection-prevention)
    - [4.1.2 XSS Prevention](#412-xss-prevention)
    - [4.1.3 Command Injection Prevention](#413-command-injection-prevention)
    - [4.1.4 CSRF Protection](#414-csrf-protection)
    - [4.1.5 Output Escaping](#415-output-escaping)
  - [4.2 API Security](#42-api-security)
    - [4.2.1 API Authentication](#421-api-authentication)
    - [4.2.2 API Authorization](#422-api-authorization)
    - [4.2.3 API Rate Limiting](#423-api-rate-limiting)
    - [4.2.4 API Gateway Security](#424-api-gateway-security)
  - [4.3 Security Headers](#43-security-headers)
    - [4.3.1 Content Security Policy (CSP)](#431-content-security-policy-csp)
    - [4.3.2 HSTS](#432-hsts)
    - [4.3.3 X-Frame-Options](#433-x-frame-options)
    - [4.3.4 X-Content-Type-Options](#434-x-content-type-options)
    - [4.3.5 Referrer Policy](#435-referrer-policy)
  - [4.4 Secure Coding Practices](#44-secure-coding-practices)
    - [4.4.1 Input Sanitization](#441-input-sanitization)
    - [4.4.2 Secure Error Handling](#442-secure-error-handling)
    - [4.4.3 Dependency Management](#443-dependency-management)
    - [4.4.4 Secure Defaults](#444-secure-defaults)
- [5. Infrastructure & Network Security](#5-infrastructure--network-security)
  - [5.1 Network Segmentation](#51-network-segmentation)
    - [5.1.1 Private Subnets](#511-private-subnets)
    - [5.1.2 DMZ Architecture](#512-dmz-architecture)
    - [5.1.3 Service Isolation](#513-service-isolation)
    - [5.1.4 Zero Trust Networking](#514-zero-trust-networking)
  - [5.2 Firewalls & WAF](#52-firewalls--waf)
    - [5.2.1 Network Firewalls](#521-network-firewalls)
    - [5.2.2 Security Groups](#522-security-groups)
    - [5.2.3 Web Application Firewall (WAF)](#523-web-application-firewall-waf)
    - [5.2.4 Traffic Filtering](#524-traffic-filtering)
  - [5.3 DDoS Protection](#53-ddos-protection)
    - [5.3.1 Traffic Scrubbing](#531-traffic-scrubbing)
    - [5.3.2 Rate Limiting](#532-rate-limiting)
    - [5.3.3 CDN Protection](#533-cdn-protection)
    - [5.3.4 Load Shedding](#534-load-shedding)
  - [5.4 Identity & Access Management (IAM)](#54-identity--access-management-iam)
    - [5.4.1 Infrastructure Roles](#541-infrastructure-roles)
    - [5.4.2 Temporary Credentials](#542-temporary-credentials)
    - [5.4.3 Least Privilege Access](#543-least-privilege-access)
    - [5.4.4 Service Accounts](#544-service-accounts)
  - [5.5 Secrets Management](#55-secrets-management)
    - [5.5.1 API Keys](#551-api-keys)
    - [5.5.2 Vault Systems](#552-vault-systems)
    - [5.5.3 Secret Rotation](#553-secret-rotation)
    - [5.5.4 Environment Variables](#554-environment-variables)
- [6. Monitoring, Auditing & Incident Response](#6-monitoring-auditing--incident-response)
  - [6.1 Audit Logging](#61-audit-logging)
    - [6.1.1 Authentication Logs](#611-authentication-logs)
    - [6.1.2 Authorization Logs](#612-authorization-logs)
    - [6.1.3 Administrative Actions](#613-administrative-actions)
    - [6.1.4 Immutable Logs](#614-immutable-logs)
  - [6.2 Security Monitoring](#62-security-monitoring)
    - [6.2.1 Intrusion Detection](#621-intrusion-detection)
    - [6.2.2 Threat Monitoring](#622-threat-monitoring)
    - [6.2.3 Anomaly Detection](#623-anomaly-detection)
    - [6.2.4 SIEM Systems](#624-siem-systems)
  - [6.3 Incident Response](#63-incident-response)
    - [6.3.1 Detection](#631-detection)
    - [6.3.2 Containment](#632-containment)
    - [6.3.3 Recovery](#633-recovery)
    - [6.3.4 Postmortem Analysis](#634-postmortem-analysis)
  - [6.4 Compliance & Governance](#64-compliance--governance)
    - [6.4.1 GDPR](#641-gdpr)
    - [6.4.2 HIPAA](#642-hipaa)
    - [6.4.3 PCI-DSS](#643-pci-dss)
    - [6.4.4 SOC 2](#644-soc-2)
- [7. Security in Distributed Systems](#7-security-in-distributed-systems)
  - [7.1 Microservices Security](#71-microservices-security)
    - [7.1.1 Service Authentication](#711-service-authentication)
    - [7.1.2 Service-to-Service Encryption](#712-service-to-service-encryption)
    - [7.1.3 API Gateway Security](#713-api-gateway-security)
    - [7.1.4 Service Mesh Security](#714-service-mesh-security)
  - [7.2 Cloud Security](#72-cloud-security)
    - [7.2.1 Shared Responsibility Model](#721-shared-responsibility-model)
    - [7.2.2 Cloud IAM](#722-cloud-iam)
    - [7.2.3 Storage Security](#723-storage-security)
    - [7.2.4 Network Isolation](#724-network-isolation)
  - [7.3 Database Security](#73-database-security)
    - [7.3.1 Database Authentication](#731-database-authentication)
    - [7.3.2 Query Authorization](#732-query-authorization)
    - [7.3.3 Encryption](#733-encryption)
    - [7.3.4 Backup Security](#734-backup-security)
  - [7.4 Messaging System Security](#74-messaging-system-security)
    - [7.4.1 Secure Queues](#741-secure-queues)
    - [7.4.2 Message Encryption](#742-message-encryption)
    - [7.4.3 Access Control](#743-access-control)
    - [7.4.4 Replay Protection](#744-replay-protection)
- [8. Security Decision Framework](#8-security-decision-framework)
  - [8.1 Choosing Security Techniques](#81-choosing-security-techniques)
    - [8.1.1 Public APIs](#811-public-apis)
    - [8.1.2 Financial Systems](#812-financial-systems)
    - [8.1.3 Healthcare Systems](#813-healthcare-systems)
    - [8.1.4 Internal Enterprise Systems](#814-internal-enterprise-systems)
    - [8.1.5 Consumer Applications](#815-consumer-applications)
  - [8.2 Security Tradeoffs](#82-security-tradeoffs)
    - [8.2.1 Security vs Performance](#821-security-vs-performance)
    - [8.2.2 Security vs Usability](#822-security-vs-usability)
    - [8.2.3 Security vs Cost](#823-security-vs-cost)
    - [8.2.4 Security vs Operational Complexity](#824-security-vs-operational-complexity)
  - [8.3 Scenario → Security Technique](#83-scenario--security-technique)
    - [8.3.1 Preventing DDoS](#831-preventing-ddos)
    - [8.3.2 Protecting Sensitive Data](#832-protecting-sensitive-data)
    - [8.3.3 Securing APIs](#833-securing-apis)
    - [8.3.4 Securing Distributed Systems](#834-securing-distributed-systems)
- [9. Common Security Anti-Patterns](#9-common-security-anti-patterns)
  - [9.1 Hardcoded Secrets](#91-hardcoded-secrets)
  - [9.2 Missing TLS](#92-missing-tls)
  - [9.3 Overprivileged Access](#93-overprivileged-access)
  - [9.4 Trusting Client Input](#94-trusting-client-input)
  - [9.5 No Rate Limiting](#95-no-rate-limiting)
  - [9.6 Weak Password Policies](#96-weak-password-policies)
  - [9.7 Missing Audit Logs](#97-missing-audit-logs)
  - [9.8 Publicly Exposed Databases](#98-publicly-exposed-databases)
  - [9.9 No Secret Rotation](#99-no-secret-rotation)
  - [9.10 Ignoring Dependency Vulnerabilities](#910-ignoring-dependency-vulnerabilities)
- [10. Security Patterns by System Type](#10-security-patterns-by-system-type)
  - [10.1 Summary: Security Techniques by Pattern](#101-summary-security-techniques-by-pattern)
  - [10.2 Pattern A – CRUD & Storage Systems (Security Focus)](#102-pattern-a---crud--storage-systems-security-focus)
  - [10.3 Pattern B – Feed / Timeline Systems (Security Focus)](#103-pattern-b---feed--timeline-systems-security-focus)
  - [10.4 Pattern C – Real-Time Systems (Security Focus)](#104-pattern-c---real-time-systems-security-focus)
  - [10.5 Pattern D – Search Systems (Security Focus)](#105-pattern-d---search-systems-security-focus)
  - [10.6 Pattern E – Geospatial Systems (Security Focus)](#106-pattern-e---geospatial-systems-security-focus)
  - [10.7 Pattern F – Financial Systems (Security Focus)](#107-pattern-f---financial-systems-security-focus)
  - [10.8 Pattern G – Large-Scale Infrastructure (Security Focus)](#108-pattern-g---large-scale-infrastructure-security-focus)
  - [10.9 Pattern H – Collaborative Systems (Security Focus)](#109-pattern-h---collaborative-systems-security-focus)
- [11. Comparison of the Four Pillars](#11-comparison-of-the-four-pillars)
  - [11.1 Scalability](#111-scalability)
    - [11.1.1 Handling Growth](#1111-handling-growth)
    - [11.1.2 Throughput Optimization](#1112-throughput-optimization)
  - [11.2 Availability](#112-availability)
    - [11.2.1 System Uptime](#1121-system-uptime)
    - [11.2.2 Fault Tolerance](#1122-fault-tolerance)
  - [11.3 Durability](#113-durability)
    - [11.3.1 Data Persistence](#1131-data-persistence)
    - [11.3.2 Crash Recovery](#1132-crash-recovery)
  - [11.4 Security](#114-security)
    - [11.4.1 Confidentiality](#1141-confidentiality)
    - [11.4.2 Integrity](#1142-integrity)
    - [11.4.3 Access Protection](#1143-access-protection)
  - [11.5 Tradeoff Matrix](#115-tradeoff-matrix)
    - [11.5.1 Scalability vs Security](#1151-scalability-vs-security)
    - [11.5.2 Availability vs Security](#1152-availability-vs-security)
    - [11.5.3 Durability vs Performance](#1153-durability-vs-performance)
- [12. Interview-Oriented HLD Notes](#12-interview-oriented-hld-notes)
  - [12.1 Key Tradeoffs](#121-key-tradeoffs)
    - [12.1.1 Security vs Performance](#1211-security-vs-performance)
    - [12.1.2 Security vs User Experience](#1212-security-vs-user-experience)
    - [12.1.3 Security vs Scalability](#1213-security-vs-scalability)
    - [12.1.4 Security vs Operational Simplicity](#1214-security-vs-operational-simplicity)
  - [12.2 Common Interview Questions](#122-common-interview-questions)
    - [12.2.1 How Would You Secure APIs?](#1221-how-would-you-secure-apis)
    - [12.2.2 How Does OAuth Work?](#1222-how-does-oauth-work)
    - [12.2.3 How to Store Passwords Securely?](#1223-how-to-store-passwords-securely)
    - [12.2.4 How to Prevent SQL Injection?](#1224-how-to-prevent-sql-injection)
    - [12.2.5 How to Secure Microservices?](#1225-how-to-secure-microservices)
  - [12.3 Quick Revision Material](#123-quick-revision-material)
    - [12.3.1 Security Cheat Sheet](#1231-security-cheat-sheet)
    - [12.3.2 AuthN vs AuthZ](#1232-authn-vs-authz)
    - [12.3.3 Encryption Comparison Table](#1233-encryption-comparison-table)
    - [12.3.4 Key Interview Sentences](#1234-key-interview-sentences)
    - [12.3.5 Self-Test Questions](#1235-self-test-questions)

---

## 1. Security Fundamentals

### 1.1 Why Security Matters in HLD

Security is not an afterthought – it must be baked into every layer of a high‑level design. It protects data confidentiality, integrity, and availability against unauthorized access, tampering, disclosure, and denial‑of‑service attacks.

#### 1.1.1 Protecting User Data
Users trust the system with personal information, financial details, and private communications. A breach erodes trust and can have legal and financial consequences.

#### 1.1.2 Preventing Unauthorized Access
Without proper authentication and authorization, attackers can impersonate users, access sensitive resources, and manipulate data.

#### 1.1.3 Ensuring System Integrity
Data and code must remain unaltered by malicious actors. Integrity failures can lead to incorrect business logic, corrupted data, and backdoors.

#### 1.1.4 Maintaining Availability
Security measures like DDoS protection and rate limiting ensure the system remains available to legitimate users even under attack.

#### 1.1.5 Regulatory Compliance
Laws and standards (GDPR, HIPAA, PCI‑DSS, SOC 2) mandate specific security controls. Non‑compliance can result in massive fines and loss of operating licenses.

### 1.2 Core Security Principles

#### 1.2.1 Defense in Depth
Layer multiple security controls so that if one fails, others still protect the system. Example: firewall + WAF + application authentication + database encryption.

#### 1.2.2 Least Privilege
Every user, service, and process should have only the minimum permissions necessary to perform its function. This limits the damage from compromised accounts.

#### 1.2.3 Zero Trust
Never trust any network, device, or user by default. Always verify identity and authorization, even for internal communication. Enforce mTLS and continuous authentication.

#### 1.2.4 Secure by Default
Systems should be configured securely out‑of‑the‑box. Insecure defaults (e.g., open ports, default passwords) must be changed before production use.

#### 1.2.5 Principle of Separation
Separate duties and environments: development vs. production, read vs. write access, user data vs. system data. This prevents cross‑contamination and limits blast radius.

#### 1.2.6 Fail Securely
When a security control fails (e.g., a firewall crashes), the system should default to a secure state (deny access) rather than an open state (allow all).

### 1.3 CIA Triad

#### 1.3.1 Confidentiality
Ensuring that data is accessible only to those authorized to view it. Achieved through encryption, access controls, and data masking.

#### 1.3.2 Integrity
Ensuring that data is accurate and has not been tampered with. Achieved through hashing, digital signatures, checksums, and input validation.

#### 1.3.3 Availability
Ensuring that the system and data are available when needed. Achieved through redundancy, DDoS protection, and fault‑tolerant design.

---

## 2. Authentication & Authorization

### 2.1 Authentication (AuthN)

Verifying the identity of a user, service, or device. “Who are you?”

#### 2.1.1 Username & Password
The most common method. Must be combined with strong hashing (bcrypt, Argon2) and password policies. Weak on its own; pair with MFA.

#### 2.1.2 Multi-Factor Authentication (MFA)
Requires at least two independent factors: something you know (password), something you have (TOTP, security key), something you are (biometrics). Stops 99.9% of account takeovers.

#### 2.1.3 OAuth 2.0
A delegation protocol allowing third‑party applications to access resources on behalf of a user without sharing credentials. Uses scoped access tokens.

#### 2.1.4 OpenID Connect (OIDC)
An authentication layer built on OAuth 2.0. Provides identity information via an ID token (JWT). Used for “Sign in with Google/Facebook”.

#### 2.1.5 Single Sign-On (SSO)
Allows users to authenticate once and access multiple applications. Typically implemented with SAML or OIDC. Improves user experience and centralizes authentication.

#### 2.1.6 Biometric Authentication
Uses fingerprints, face recognition, or iris scans. Convenient and hard to forge, but must be combined with a fallback and careful template storage.

### 2.2 Authorization (AuthZ)

Determining what an authenticated identity is allowed to do. “What can you do?”

#### 2.2.1 Role-Based Access Control (RBAC)
Assign roles to users, and permissions to roles. Example: `role=admin` can create users; `role=viewer` can only read.

#### 2.2.2 Attribute-Based Access Control (ABAC)
Policies based on attributes of the user, resource, and environment. More flexible than RBAC. Example: “Allow read if `user.department == resource.owner` and `time.now() between 9‑5`”.

#### 2.2.3 Fine-Grained Permissions
Instead of broad roles, assign permissions per resource or operation. Used in cloud IAM: `s3:GetObject` on a specific bucket prefix.

#### 2.2.4 Resource-Level Authorization
Access decisions are made at the individual data object level. Example: a user can only see their own documents, enforced by the application or database.

### 2.3 Session & Token Security

#### 2.3.1 JWT (JSON Web Tokens)
A compact, self‑contained token for transmitting claims between parties. Stateless; the server verifies the signature without a DB lookup. Must be protected against tampering and leakage.

#### 2.3.2 Access Tokens
Short‑lived tokens (minutes to hours) used to access protected resources. Typically bearer tokens – must be sent over HTTPS.

#### 2.3.3 Refresh Tokens
Longer‑lived tokens used to obtain new access tokens without re‑authentication. Must be stored securely (HttpOnly, Secure cookie) and rotated.

#### 2.3.4 Session Expiration
Sessions should expire after a period of inactivity (idle timeout) and have an absolute maximum lifetime. Reduces window for session hijacking.

#### 2.3.5 Secure Cookie Handling
Set cookies with `Secure`, `HttpOnly`, and `SameSite` flags. Prevents XSS from stealing session cookies and CSRF attacks.

---

## 3. Encryption & Data Protection

### 3.1 Encryption Fundamentals

#### 3.1.1 Symmetric Encryption
Same key for encryption and decryption. Fast, used for bulk data (AES‑256). Key distribution is the challenge.

#### 3.1.2 Asymmetric Encryption
Uses a public/private key pair. Public key encrypts, private key decrypts. Used for key exchange, digital signatures. Slower than symmetric.

#### 3.1.3 Hashing
One‑way function that maps data to a fixed‑size digest (SHA‑256, bcrypt). Used for password storage, integrity checks. Not reversible.

#### 3.1.4 Digital Signatures
A hash of the data encrypted with the sender’s private key. Verifies authenticity and integrity. Used in certificates, JWTs.

### 3.2 Encryption at Rest

#### 3.2.1 Disk Encryption
Full‑disk encryption (BitLocker, LUKS) protects against physical theft of drives. Transparent to applications.

#### 3.2.2 Database Encryption
Transparent Data Encryption (TDE) encrypts the entire database at the storage level. For sensitive fields, use application‑level encryption with separate keys.

#### 3.2.3 Object Storage Encryption
Cloud providers offer server‑side encryption (SSE‑S3, SSE‑KMS) and client‑side encryption. Data is encrypted before being written to disk.

#### 3.2.4 Key Management
Use a dedicated key management service (AWS KMS, HashiCorp Vault) to generate, store, and rotate encryption keys. Keys should never be hardcoded.

### 3.3 Encryption in Transit

#### 3.3.1 TLS / HTTPS
All HTTP traffic must be over TLS 1.2 or 1.3. Encrypts data between client and server, preventing eavesdropping and man‑in‑the‑middle attacks.

#### 3.3.2 TLS Handshake
Client and server agree on cipher suite, authenticate (optionally mutual), and exchange keys. Session resumption (tickets) reduces latency.

#### 3.3.3 Certificate Management
Use a certificate authority (Let’s Encrypt, AWS ACM) to issue and renew certificates. Automate renewal to prevent expiration.

#### 3.3.4 Mutual TLS (mTLS)
Both client and server present certificates. Used for service‑to‑service authentication inside a trusted network. Enables zero‑trust microservices.

### 3.4 Data Protection Techniques

#### 3.4.1 Data Masking
Replace sensitive data with a masked version in logs, UIs, and non‑production environments. Example: credit card `****‑****‑****‑1234`.

#### 3.4.2 Tokenization
Replace sensitive data with a non‑sensitive token. The mapping is stored in a secure vault. Example: payment gateways return `tok_abc123` instead of the real card number.

#### 3.4.3 PII Protection
Personally Identifiable Information must be encrypted, anonymized, or pseudonymized. Access must be tightly controlled and logged.

#### 3.4.4 Field-Level Encryption
Encrypt specific database columns (e.g., SSN, credit card) at the application level. The database never sees plaintext.

---

## 4. Application Security

### 4.1 Input Validation & Output Encoding

#### 4.1.1 SQL Injection Prevention
Use **parameterized queries** (prepared statements) exclusively. Never concatenate user input into SQL strings.

#### 4.1.2 XSS Prevention
Encode all output based on context (HTML entity encoding for HTML, JavaScript encoding for JS). Use Content Security Policy headers.

#### 4.1.3 Command Injection Prevention
Avoid invoking OS commands with user input. If unavoidable, use safe APIs (e.g., `subprocess.run` with list of arguments, never `shell=True`).

#### 4.1.4 CSRF Protection
Use anti‑CSRF tokens generated server‑side and validated on state‑changing requests. SameSite cookies provide additional defense.

#### 4.1.5 Output Escaping
Always escape output to the correct context: HTML, JavaScript, CSS, URL. Use libraries like OWASP Java Encoder.

### 4.2 API Security

#### 4.2.1 API Authentication
Require OAuth2 tokens, API keys, or mTLS for all endpoints. Never expose unauthenticated endpoints unless intended.

#### 4.2.2 API Authorization
Validate scopes and permissions on every request. A token might be valid but lack permission for the specific operation.

#### 4.2.3 API Rate Limiting
Apply token bucket or sliding window rate limiting per user, IP, or API key to prevent abuse and DDoS.

#### 4.2.4 API Gateway Security
An API gateway (Kong, Apigee, AWS API Gateway) centralizes authentication, authorization, rate limiting, and request validation. It hides internal services.

### 4.3 Security Headers

#### 4.3.1 Content Security Policy (CSP)
Restricts sources of scripts, styles, and other resources. Mitigates XSS by preventing execution of inline scripts and untrusted sources.

#### 4.3.2 HSTS
`Strict‑Transport‑Security` forces the browser to use HTTPS only, preventing protocol downgrade attacks.

#### 4.3.3 X-Frame-Options
Prevents the site from being embedded in an iframe, stopping clickjacking.

#### 4.3.4 X-Content-Type-Options
`nosniff` prevents the browser from MIME‑sniffing and interpreting files as a different content type.

#### 4.3.5 Referrer Policy
Controls how much referrer information is sent when navigating. `strict‑origin‑when‑cross‑origin` is a good default.

### 4.4 Secure Coding Practices

#### 4.4.1 Input Sanitization
Treat all user input as untrusted. Validate type, length, format, and range. Use allowlists rather than blocklists.

#### 4.4.2 Secure Error Handling
Never expose stack traces or internal details to clients. Use generic error messages; log details server‑side.

#### 4.4.3 Dependency Management
Regularly scan dependencies for known vulnerabilities (CVE). Use tools like Dependabot, Snyk. Keep libraries updated.

#### 4.4.4 Secure Defaults
Frameworks should default to secure settings (e.g., auto‑escaping templates, CSRF protection enabled). Developers must consciously disable security, not enable it.

---

## 5. Infrastructure & Network Security

### 5.1 Network Segmentation

#### 5.1.1 Private Subnets
Place databases and internal services in private subnets with no direct internet access. Only allow necessary traffic.

#### 5.1.2 DMZ Architecture
A demilitarized zone separates public‑facing services (load balancers, web servers) from internal trusted networks. Adds a buffer layer.

#### 5.1.3 Service Isolation
Use separate VPCs or network policies to isolate different environments (prod, staging) and tenants. Limits lateral movement.

#### 5.1.4 Zero Trust Networking
Micro‑segment the network and require authentication for every connection, even inside the perimeter. Technologies: service mesh (Istio), software‑defined perimeters.

### 5.2 Firewalls & WAF

#### 5.2.1 Network Firewalls
Control inbound and outbound traffic based on IP, port, and protocol. Cloud security groups (AWS, Azure) act as virtual firewalls.

#### 5.2.2 Security Groups
Stateful, allow‑list based rules attached to instances. Deny all by default; explicitly allow required traffic.

#### 5.2.3 Web Application Firewall (WAF)
Inspects HTTP traffic at layer 7. Blocks common attacks (SQLi, XSS, path traversal) using managed rule sets. Example: AWS WAF, Cloudflare WAF.

#### 5.2.4 Traffic Filtering
Block malicious IPs, geographies, or patterns using WAF rules and network ACLs.

### 5.3 DDoS Protection

#### 5.3.1 Traffic Scrubbing
Cloud‑based scrubbing centers filter out attack traffic while passing legitimate requests. AWS Shield Advanced, Cloudflare Magic Transit.

#### 5.3.2 Rate Limiting
Limits the number of requests from a single source. Prevents volumetric attacks at the edge (CDN, WAF).

#### 5.3.3 CDN Protection
CDNs absorb and distribute traffic globally, making it harder to overwhelm a single origin server.

#### 5.3.4 Load Shedding
When under extreme load, shed non‑critical requests to preserve core functionality (e.g., drop analytics, keep checkout).

### 5.4 Identity & Access Management (IAM)

#### 5.4.1 Infrastructure Roles
Define roles with precise permissions for cloud resources. Attach roles to instances, services, and users – never use long‑term credentials.

#### 5.4.2 Temporary Credentials
Use short‑lived tokens (STS, OIDC) that automatically expire. Reduces risk of credential leakage.

#### 5.4.3 Least Privilege Access
Grant only the permissions required to perform a task. Regularly review and right‑size permissions.

#### 5.4.4 Service Accounts
Machine identities for applications and scripts. Use dedicated service accounts with minimal privileges, not personal accounts.

### 5.5 Secrets Management

#### 5.5.1 API Keys
Use scoped, revocable API keys. Never embed in client‑side code. Rotate regularly.

#### 5.5.2 Vault Systems
HashiCorp Vault, AWS Secrets Manager, Azure Key Vault. Securely store, access, and audit secrets. Provide dynamic secrets for databases.

#### 5.5.3 Secret Rotation
Automatically rotate secrets (e.g., DB passwords, API keys) without downtime. Vault dynamic secrets or rolling updates.

#### 5.5.4 Environment Variables
Avoid storing secrets in plain environment variables. Use a secrets manager injection via sidecar or init container.

---

## 6. Monitoring, Auditing & Incident Response

### 6.1 Audit Logging

#### 6.1.1 Authentication Logs
Record all login attempts (success/failure), IP, timestamp, MFA status. Used for brute‑force detection and account compromise investigation.

#### 6.1.2 Authorization Logs
Log access decisions: who accessed what resource, when, and with which permissions.

#### 6.1.3 Administrative Actions
Record all privileged operations (user creation, permission changes, data deletion). Critical for compliance and forensics.

#### 6.1.4 Immutable Logs
Store logs in write‑once‑read‑many (WORM) storage. Prevents attackers from covering their tracks. S3 Object Lock, Cloud Logging retention.

### 6.2 Security Monitoring

#### 6.2.1 Intrusion Detection
Network and host‑based IDS (Snort, OSSEC) detect malicious activity patterns. Cloud‑native solutions: AWS GuardDuty, Azure Security Center.

#### 6.2.2 Threat Monitoring
Continuously monitor for indicators of compromise (IOCs) and threat intelligence feeds.

#### 6.2.3 Anomaly Detection
Use machine learning to detect unusual behavior (e.g., data exfiltration, impossible travel). Alert on deviations from baseline.

#### 6.2.4 SIEM Systems
Security Information and Event Management (Splunk, ELK, Sentinel) aggregates logs, correlates events, and triggers alerts.

### 6.3 Incident Response

#### 6.3.1 Detection
Identify potential incidents via monitoring, alerts, and user reports. Establish severity levels.

#### 6.3.2 Containment
Isolate affected systems, revoke compromised credentials, block malicious IPs to prevent further damage.

#### 6.3.3 Recovery
Restore from clean backups, patch vulnerabilities, rebuild compromised instances. Verify integrity before returning to service.

#### 6.3.4 Postmortem Analysis
Conduct a blameless postmortem to identify root cause, improve processes, and implement preventive controls.

### 6.4 Compliance & Governance

#### 6.4.1 GDPR
European data protection regulation. Requires data minimization, user consent, right to erasure, breach notification, and data residency controls.

#### 6.4.2 HIPAA
U.S. healthcare regulation. Mandates administrative, physical, and technical safeguards for protected health information (PHI).

#### 6.4.3 PCI-DSS
Payment Card Industry Data Security Standard. Requires network segmentation, encryption, access control, and regular penetration testing for cardholder data.

#### 6.4.4 SOC 2
Service Organization Control report on security, availability, processing integrity, confidentiality, and privacy. Common for SaaS providers.

---

## 7. Security in Distributed Systems

### 7.1 Microservices Security

#### 7.1.1 Service Authentication
Use mTLS or JWT tokens for service‑to‑service calls. SPIFFE/SPIRE provides standardized identity for workloads.

#### 7.1.2 Service-to-Service Encryption
All inter‑service traffic should be encrypted with TLS. Service mesh sidecars (Envoy) handle this transparently.

#### 7.1.3 API Gateway Security
The gateway is the single entry point for external clients. It handles authentication, authorization, rate limiting, and request routing.

#### 7.1.4 Service Mesh Security
Istio, Linkerd, and Consul Connect provide mutual TLS, fine‑grained authorization policies, and observability without application changes.

### 7.2 Cloud Security

#### 7.2.1 Shared Responsibility Model
Cloud provider secures the infrastructure (physical, network, hypervisor). Customer secures their data, applications, and access management.

#### 7.2.2 Cloud IAM
Define who can access which cloud resources. Use roles, policies, and resource‑based permissions. Enable multi‑factor authentication for all human users.

#### 7.2.3 Storage Security
Encrypt data at rest (server‑side or client‑side). Restrict bucket access with IAM policies and block public access. Enable logging and versioning.

#### 7.2.4 Network Isolation
Use VPCs, private subnets, security groups, and VPC endpoints to limit exposure. Never expose databases directly to the internet.

### 7.3 Database Security

#### 7.3.1 Database Authentication
Use strong, unique passwords or IAM roles. Never use default credentials. Integrate with LDAP or cloud IAM where possible.

#### 7.3.2 Query Authorization
Restrict which users/applications can execute which queries. Use read‑only replicas for reporting. Parameterize all queries.

#### 7.3.3 Encryption
Enable TDE for data at rest. Use TLS for all connections. For highly sensitive fields, apply application‑level encryption.

#### 7.3.4 Backup Security
Encrypt database backups and store them in a separate, secured account. Restrict access to backup restore functions.

### 7.4 Messaging System Security

#### 7.4.1 Secure Queues
Use TLS for connections to Kafka, RabbitMQ, SQS. Enable authentication (SASL/SCRAM, IAM) and authorization (ACLs, policies).

#### 7.4.2 Message Encryption
Encrypt message payloads end‑to‑end if the broker is not fully trusted. Kafka supports SSL and client‑side encryption.

#### 7.4.3 Access Control
Kafka ACLs restrict which users/producers can read/write to topics. SQS policies restrict queue operations.

#### 7.4.4 Replay Protection
Use unique message IDs and idempotent consumers to prevent replay attacks or duplicate processing.

---

## 8. Security Decision Framework

### 8.1 Choosing Security Techniques

#### 8.1.1 Public APIs
Require API keys or OAuth tokens. Enforce HTTPS and rate limiting. Validate all inputs. Publish a security contact.

#### 8.1.2 Financial Systems
Strong MFA, short‑lived tokens, encryption at rest and in transit, PCI‑DSS compliance, immutable audit logs, fraud detection.

#### 8.1.3 Healthcare Systems
HIPAA compliance, PHI encryption, access control with audit trails, secure APIs, data anonymization for analytics.

#### 8.1.4 Internal Enterprise Systems
SSO with MFA, RBAC, network segmentation (VPN/Zero Trust), endpoint protection, data loss prevention.

#### 8.1.5 Consumer Applications
OAuth2/OIDC for social login, passwordless options, secure cookie handling, CSP and security headers, GDPR compliance.

### 8.2 Security Tradeoffs

#### 8.2.1 Security vs Performance
Encryption and logging add CPU and latency overhead. Balance by using hardware acceleration (AES‑NI), session resumption, and optimized algorithms.

#### 8.2.2 Security vs Usability
Strict MFA and complex passwords frustrate users. Use adaptive MFA (step‑up for risky actions), biometrics, and single sign‑on.

#### 8.2.3 Security vs Cost
Advanced security tools (HSM, dedicated WAF, SIEM) are expensive. Prioritize based on risk assessment and compliance requirements.

#### 8.2.4 Security vs Operational Complexity
Managing secrets, certificates, and IAM policies adds operational burden. Use managed services and automation to reduce toil.

### 8.3 Scenario → Security Technique

#### 8.3.1 Preventing DDoS
Anycast CDN, WAF rate limiting, traffic scrubbing, auto‑scaling, load shedding.

#### 8.3.2 Protecting Sensitive Data
Encryption at rest and in transit, tokenization, data masking, field‑level encryption, strict access controls, immutable audit logs.

#### 8.3.3 Securing APIs
OAuth2/OIDC, HTTPS, input validation, rate limiting, API gateway, security headers, CORS policy.

#### 8.3.4 Securing Distributed Systems
mTLS for service‑to‑service, zero trust networking, centralized secrets management, immutable infrastructure, continuous vulnerability scanning.

---

## 9. Common Security Anti-Patterns

### 9.1 Hardcoded Secrets
Embedding API keys or passwords in source code. Use a secrets manager instead.

### 9.2 Missing TLS
Serving traffic over HTTP. All communications must use TLS, even internally.

### 9.3 Overprivileged Access
Granting broad admin permissions when read‑only or scoped access is sufficient. Apply least privilege.

### 9.4 Trusting Client Input
Assuming client‑side validation is sufficient. Always validate and sanitize on the server.

### 9.5 No Rate Limiting
Exposing endpoints without throttling invites abuse, brute‑force, and DDoS.

### 9.6 Weak Password Policies
Allowing short passwords, no MFA, or not enforcing password rotation. Use strong hashing (Argon2) and MFA.

### 9.7 Missing Audit Logs
No record of who did what, making incidents impossible to investigate. Log all security events.

### 9.8 Publicly Exposed Databases
Opening databases to the internet without proper authentication. Use private subnets and strict access controls.

### 9.9 No Secret Rotation
Using the same credentials indefinitely. Rotate regularly and automate the process.

### 9.10 Ignoring Dependency Vulnerabilities
Running outdated libraries with known CVEs. Regularly scan and update dependencies.

---

## 10. Security Patterns by System Type

### 10.1 Summary: Security Techniques by Pattern

| Pattern | System Type | Key Security Techniques | Deep Dive / Rationale |
|---------|-------------|------------------------|------------------------|
| **A: CRUD & Storage Systems** | URL Shortener, Pastebin, Online Bookstore, Ticket Booking, Dropbox / Drive | Authentication; RBAC; Database Encryption | User/password with MFA; OAuth2 for third‑party access. JWT tokens with short expiry. Roles for admin, editor, viewer. Fine‑grained permissions for individual resources (e.g., a specific paste or file). TDE for inventory… |
| **B: Feed / Timeline Systems** | Twitter / X, Instagram Feed, Facebook News Feed, LinkedIn Feed | Session Security; CDN Protection; Abuse Prevention | Secure cookies, CSRF tokens, OIDC for social login. JWT with refresh token rotation. WAF in front of CDN, signed URLs for private media, DDoS absorption. Rate limiting on post creation, CAPTCHA for suspicious activity,… |
| **C: Real-Time Systems** | WhatsApp / Messenger, Slack / Discord, Live Comments (YouTube Live), Multiplayer Game Backend | WebSocket Security; Token Validation; Rate Limiting | Authenticate the WebSocket upgrade request with a token. Validate token on every message or connection. Short‑lived tokens for chat/presence; refresh tokens for longer sessions. Limit messages per second per user to… |
| **D: Search Systems** | Google Search, YouTube Search, Amazon Product Search, Search Autocomplete | Query Validation; API Security; Access Control | Sanitize search input; avoid exposing raw error messages. Prevent injection attacks. Use API keys or OAuth for search API. Apply rate limiting to prevent scraping. Ensure search results only include documents the user… |
| **E: Geospatial Systems** | Uber / Lyft, Google Maps, Food Delivery, Nearby Friends | Location Privacy; Real-Time Access Control; Secure Device Communication | Anonymize or aggregate location data. Allow users to opt‑out of location tracking. Ensure only the rider and assigned driver can see each other’s real‑time location. Mutual TLS for device‑to‑server; certificate pinning… |
| **F: Financial Systems** | Payment Gateway, Wallet System, UPI-like System, Distributed Job Scheduler | MFA; Strong Encryption; Fraud Detection; Immutable Audit Logs | Mandatory for all user‑facing actions. Hardware security keys for high‑value transactions. AES‑256 for data at rest, TLS 1.3 for transit, PCI‑DSS compliant tokenization for card data. Real‑time machine learning models… |
| **G: Large-Scale Infrastructure** | Analytics / Clickstream, Distributed Logging (ELK), Metrics Monitoring, Kafka-like System | IAM; Secret Management; Infrastructure Isolation | Fine‑grained roles for all infrastructure components. Use instance roles and workload identity. Vault for all secrets; dynamic credentials for databases; no long‑lived keys. Separate accounts/projects for prod, staging,… |
| **H: Collaborative Systems** | Google Docs, Figma / Whiteboard, Distributed Locking Service, Web Crawler | Shared Access Permissions; Secure Collaboration; Version Audit Trails | Fine‑grained sharing: view, comment, edit. Link‑based sharing with optional passwords and expiration. End‑to‑end encryption for sensitive documents; real‑time sync over encrypted WebSocket. Complete history of document… |

---

### 10.2 Pattern A – CRUD & Storage Systems (Security Focus)

| System | Key Security Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **URL Shortener** | Authentication | User/password with MFA; OAuth2 for third‑party access. | JWT tokens with short expiry. |
| **Pastebin** | RBAC | Roles for admin, editor, viewer. | Fine‑grained permissions for individual resources (e.g., a specific paste or file). |
| **Online Bookstore** | Database Encryption | TDE for inventory DB, application‑level encryption for sensitive fields. | TLS for DB connections. |

---

### 10.3 Pattern B – Feed / Timeline Systems (Security Focus)

| System | Key Security Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Twitter / X** | Session Security | Secure cookies, CSRF tokens, OIDC for social login. | JWT with refresh token rotation. |
| **Instagram Feed** | CDN Protection | WAF in front of CDN, signed URLs for private media, DDoS absorption. | WAF in front of CDN, signed URLs for private media, DDoS absorption. |
| **Facebook News Feed** | Abuse Prevention | Rate limiting on post creation, CAPTCHA for suspicious activity, spam detection models. | Rate limiting on post creation, CAPTCHA for suspicious activity, spam detection models. |

---

### 10.4 Pattern C – Real-Time Systems (Security Focus)

| System | Key Security Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **WhatsApp / Messenger** | WebSocket Security | Authenticate the WebSocket upgrade request with a token. | Validate token on every message or connection. |
| **Slack / Discord** | Token Validation | Short‑lived tokens for chat/presence; refresh tokens for longer sessions. | Short‑lived tokens for chat/presence; refresh tokens for longer sessions. |
| **Live Comments (YouTube Live)** | Rate Limiting | Limit messages per second per user to prevent flooding. | Limit messages per second per user to prevent flooding. |

---

### 10.5 Pattern D – Search Systems (Security Focus)

| System | Key Security Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Google Search** | Query Validation | Sanitize search input; avoid exposing raw error messages. | Prevent injection attacks. |
| **YouTube Search** | API Security | Use API keys or OAuth for search API. | Apply rate limiting to prevent scraping. |
| **Amazon Product Search** | Access Control | Ensure search results only include documents the user is authorized to view. | Ensure search results only include documents the user is authorized to view. |

---

### 10.6 Pattern E – Geospatial Systems (Security Focus)

| System | Key Security Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Uber / Lyft** | Location Privacy | Anonymize or aggregate location data. | Allow users to opt‑out of location tracking. |
| **Google Maps** | Real-Time Access Control | Ensure only the rider and assigned driver can see each other’s real‑time location. | Ensure only the rider and assigned driver can see each other’s real‑time location. |
| **Food Delivery** | Secure Device Communication | Mutual TLS for device‑to‑server; certificate pinning in mobile apps. | Mutual TLS for device‑to‑server; certificate pinning in mobile apps. |

---

### 10.7 Pattern F – Financial Systems (Security Focus)

| System | Key Security Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Payment Gateway** | MFA | Mandatory for all user‑facing actions. | Hardware security keys for high‑value transactions. |
| **Wallet System** | Strong Encryption | AES‑256 for data at rest, TLS 1.3 for transit, PCI‑DSS compliant tokenization for card data. | AES‑256 for data at rest, TLS 1.3 for transit, PCI‑DSS compliant tokenization for card data. |
| **UPI-like System** | Fraud Detection | Real‑time machine learning models analyzing transaction patterns, device fingerprinting. | Real‑time machine learning models analyzing transaction patterns, device fingerprinting. |
| **Distributed Job Scheduler** | Immutable Audit Logs | Every financial event logged to a WORM store for compliance and forensics. | Every financial event logged to a WORM store for compliance and forensics. |

---

### 10.8 Pattern G – Large-Scale Infrastructure (Security Focus)

| System | Key Security Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Analytics / Clickstream** | IAM | Fine‑grained roles for all infrastructure components. | Use instance roles and workload identity. |
| **Distributed Logging (ELK)** | Secret Management | Vault for all secrets; dynamic credentials for databases; no long‑lived keys. | Vault for all secrets; dynamic credentials for databases; no long‑lived keys. |
| **Metrics Monitoring** | Infrastructure Isolation | Separate accounts/projects for prod, staging, dev. | Network segmentation with VPCs and security groups. |

---

### 10.9 Pattern H – Collaborative Systems (Security Focus)

| System | Key Security Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Google Docs** | Shared Access Permissions | Fine‑grained sharing: view, comment, edit. | Link‑based sharing with optional passwords and expiration. |
| **Figma / Whiteboard** | Secure Collaboration | End‑to‑end encryption for sensitive documents; real‑time sync over encrypted WebSocket. | End‑to‑end encryption for sensitive documents; real‑time sync over encrypted WebSocket. |
| **Distributed Locking Service** | Version Audit Trails | Complete history of document changes with author and timestamp. | Immutable, exportable. --- |

---

---

## 11. Comparison of the Four Pillars

### 11.1 Scalability

#### 11.1.1 Handling Growth
Ability to increase capacity by adding resources. Techniques: horizontal scaling, partitioning, caching.

#### 11.1.2 Throughput Optimization
Maximizing requests per second while maintaining latency. Techniques: async processing, load balancing.

### 11.2 Availability

#### 11.2.1 System Uptime
Percentage of time the system is operational. Techniques: redundancy, failover, health checks.

#### 11.2.2 Fault Tolerance
Continuing operation during component failures. Techniques: active‑active, circuit breakers, retries.

### 11.3 Durability

#### 11.3.1 Data Persistence
Guarantee that committed writes survive crashes. Techniques: WAL, replication, erasure coding.

#### 11.3.2 Crash Recovery
Ability to restore data to a consistent state after a failure. Techniques: WAL replay, PITR, backups.

### 11.4 Security

#### 11.4.1 Confidentiality
Ensuring data is only seen by authorized entities. Techniques: encryption, access control, data masking.

#### 11.4.2 Integrity
Ensuring data is accurate and unaltered. Techniques: hashing, signatures, input validation.

#### 11.4.3 Access Protection
Preventing unauthorized use of the system. Techniques: authentication, authorization, rate limiting.

### 11.5 Tradeoff Matrix

| Aspect | Scalability | Availability | Durability | Security |
|--------|-------------|--------------|------------|----------|
| **Primary question** | Can it handle more load? | Is it up and reachable? | Is my data safe once written? | Is my data and system safe from attackers? |
| **Key technique** | Horizontal scaling | Redundancy + failover | WAL + replication | AuthN/Z, encryption, least privilege |
| **Typical metric** | Requests/sec | Uptime % | Data loss probability | Time to detect/respond |
| **Trade‑off** | Consistency (CAP) | Consistency (CAP) | Performance / cost | Usability / performance |

#### 11.5.1 Scalability vs Security
Scaling may require replicating data across more nodes, increasing the attack surface. Security controls (TLS termination, WAF) can add latency. Balance by embedding security into the scaling design.

#### 11.5.2 Availability vs Security
Strict security can reduce availability (e.g., blocking during DDoS, rejecting stale tokens). Design for graceful degradation: maintain core functionality securely even under attack.

#### 11.5.3 Durability vs Performance
Synchronous replication for durability increases latency. Asynchronous methods improve performance but risk data loss. Choose based on business criticality.

---

## 12. Interview-Oriented HLD Notes

### 12.1 Key Tradeoffs

#### 12.1.1 Security vs Performance
Encryption, logging, and deep inspection consume CPU and add latency. Use hardware acceleration, session caching, and tune WAF rules.

#### 12.1.2 Security vs User Experience
MFA and strict password policies can frustrate users. Use adaptive authentication, biometrics, and SSO to reduce friction.

#### 12.1.3 Security vs Scalability
Centralized security services (e.g., a single authentication service) can become bottlenecks. Distribute authentication using stateless tokens (JWT) and edge verification.

#### 12.1.4 Security vs Operational Simplicity
Managing secrets, certificates, and IAM policies adds complexity. Leverage managed services (AWS KMS, ACM) and infrastructure‑as‑code.

### 12.2 Common Interview Questions

#### 12.2.1 How Would You Secure APIs?
Require HTTPS, use OAuth2 tokens or API keys, validate scopes, implement rate limiting, validate input, and use an API gateway for central enforcement.

#### 12.2.2 How Does OAuth Work?
OAuth 2.0 is a delegation protocol where a resource owner grants a client limited access to their protected resources via access tokens, without sharing credentials.

#### 12.2.3 How to Store Passwords Securely?
Use a slow, salted hashing algorithm: bcrypt, scrypt, or Argon2. Never store plaintext or use fast hashes (MD5, SHA1).

#### 12.2.4 How to Prevent SQL Injection?
Use parameterized queries (prepared statements) exclusively. Validate and sanitize all inputs. Use an ORM that handles escaping.

#### 12.2.5 How to Secure Microservices?
Implement mTLS for service‑to‑service communication, use JWT tokens or SPIFFE for identity, deploy a service mesh for policy enforcement, and apply least privilege IAM roles.

### 12.3 Quick Revision Material

#### 12.3.1 Security Cheat Sheet
- **AuthN:** Verify identity (password, MFA, OAuth2, mTLS).
- **AuthZ:** Control access (RBAC, ABAC, ACLs).
- **Encryption at rest:** AES‑256, TDE, field‑level.
- **Encryption in transit:** TLS 1.3, mTLS, HSTS.
- **Input validation:** Parameterized queries, allowlists.
- **Secrets:** Vault, rotation, no hardcoding.
- **Headers:** CSP, HSTS, X‑Frame‑Options, X‑Content‑Type‑Options.
- **Monitoring:** Audit logs, SIEM, anomaly detection.

#### 12.3.2 AuthN vs AuthZ
Authentication = who you are. Authorization = what you can do. Both are required; neither is sufficient alone.

#### 12.3.3 Encryption Comparison Table
| Type | Algorithm | Use Case |
|------|-----------|----------|
| Symmetric | AES‑256 | Bulk data encryption |
| Asymmetric | RSA, ECDH | Key exchange, signatures |
| Hashing | SHA‑256, bcrypt | Integrity, password storage |

#### 12.3.4 Key Interview Sentences
> *“I follow defense in depth: network segmentation, mTLS between services, OAuth2 for API authentication, parameterized queries to prevent injection, and encryption at rest with KMS.”*

> *“For secrets management, I use HashiCorp Vault to generate dynamic database credentials and rotate them automatically, eliminating long‑lived passwords.”*

> *“I apply least privilege to IAM roles – each microservice gets a scoped role with only the permissions it needs to run, and we audit all changes.”*

#### 12.3.5 Self-Test Questions
1. What is the difference between authentication and authorization?
2. How does TLS protect data in transit, and what is the role of certificates?
3. Describe three common web application vulnerabilities and their mitigations.
4. Why is hardcoding secrets dangerous, and what is the proper alternative?
5. How would you secure a microservices architecture?

*End of consolidated notes.*
