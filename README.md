# Enterprise-IAM-Deployment-End-to-End-SAML-2.0-Identity-Federation-LCM-Blueprint

## 📌 Project Overview

This deployment project documents the engineering of a cross-domain Identity Federation architecture. Using **Okta** as a centralized enterprise Identity Provider (IdP) and **Freshdesk** as a multi-tenant cloud Service Provider (SP), this architecture demonstrates secure SAML 2.0 Single Sign-On (SSO), dynamic data mapping, access policies, and automated Identity Lifecycle Management (LCM).


## 🏢 Business & Security Justification
In a decentralized enterprise footprint, independent cloud applications introduce massive security risks, including unmonitored password reuse, lack of visible audit trails, and "orphan accounts" left behind by departed employees. 

This project solves those critical business pain points by:
* Moving authentication to a single, hardened point of entry (Okta).
* Using cryptographically signed assertions to guarantee integrity.
* Centralizing the "Joiner, Mover, Leaver" (JML) lifecycle to ensure instant, global access termination.

---

## 🚀 Step-by-Step Technical Implementation Log

### Step 1: Trust Anchor Configuration (The Federation Handshake)

* **Action:** Created a custom SAML 2.0 Integration suite within the Okta Admin Console.
* **Endpoint Provisioning:** Formulated the Assertion Consumer Service (ACS) destination targets and Audience restrictions to restrict cross-domain data movement:
  * **Single Sign-On URL:** `https://newscorp.freshdesk.com/login/saml`
  * **Audience URI (SP Entity ID):** `https://newscorp.freshdesk.com`
* **Trust Exchange:** Downloaded the unique IdP X.509 signature certificate block and Issuer URL (`http://www.okta.com/exk14brykmxVvXFPQ698`) and successfully deployed them inside the Freshworks Security Console to activate mutual cryptographic trust.

### Step 2: Adaptive Authentication Policy Constraints

* **Action:** Created an isolated app-gating policy named `High-Security App Policy`.
* **Logic Constraints:** Upgraded baseline access rules by forcing strict credential evaluation before an authentication token could be generated for the target SaaS asset.

### Step 3: Just-In-Time (JIT) Identity Schema Mapping

* **Action:** Configured explicit user profile attribute statement evaluations inside the SAML settings panel. 
* **Data Flow:** Mapped core Okta directory variables dynamically into the SAML assertion payload to build profile objects on downstream systems upon initial sign-on:
  * `FirstName` ➡️ `user.firstName`
  * `LastName` ➡️ `user.lastName`
  * `Email` ➡️ `user.email`

### Step 4: Verification of Raw SAML Token Assertions

* **Action:** Captured and inspected the live browser-redirect XML payload during token transmission.
* **Component Analysis:**
  * `<saml2:Issuer>`: Validated the identity claims originating from the authenticating IdP server.
  * `<saml2:NameID>`: Confirmed format structure passing the distinct user context (`md.tameem-intern@newscorp.com`).
  * `<saml2:Conditions>`: Confirmed 5-minute time-skew limits (`NotBefore`/`NotOnAfter` window constraints) preventing token replay attacks.

### Step 5: Identity Lifecycle Management (Emergency Offboarding Validation)

* **Action:** Modeled a high-priority "Leaver Workflow" simulation.
* **Execution:** Created a test entity (`John Doe`), bound the profile to the active application permissions instance, and executed an immediate administrative `Deactivate` command inside the central directory layer.
* **Result:** Confirmed that stripping the identity at the IdP core instantly and automatically revoked all active downstream application instances, proving perfect mitigation of data-leak vulnerabilities.

---

## 📊 Verification Architecture Evidence

### 1. Centralized Single Sign-On Portal Dashboard

<img width="1847" height="862" alt="image" src="https://github.com/user-attachments/assets/776a885c-eefc-45c5-9e35-f28895bcce4b" />

### 2. Service Provider Trust Handshake Map

<img width="857" height="878" alt="image" src="https://github.com/user-attachments/assets/306e34a3-59f0-43e8-b6cd-a4cee01adb10" />
<img width="862" height="864" alt="image" src="https://github.com/user-attachments/assets/95852e83-0bbb-4777-bc4c-bb0aabd0a023" />

### 3. Captured SAML 2.0 XML Token Assertion Payload
<img width="1559" height="641" alt="image" src="https://github.com/user-attachments/assets/b22b6859-5038-46f5-8161-459c1f26767e" />

### 4. Successful Lifecycle Leaver Deactivation Proo
