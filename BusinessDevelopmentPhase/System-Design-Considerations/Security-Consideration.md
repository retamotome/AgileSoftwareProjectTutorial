# Security Consideration 

> [!note]    
> ![BY NC ND](../../img/Cc-by-nc-sa.png)  
> Security Consideration © 2018 by Jen Yuan Pan is licensed under [Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.en).  

## Connections 

All external connections **must be protected using strong encryption mechanisms** to ensure confidentiality, integrity, and authenticity of data in transit.

* Approved protocols:
  * **HTTPS (TLS 1.2 or above)**
  * **Secure WebSocket (WSS)**
* **Plain-text communication protocols (e.g., HTTP, Telnet, FTP) are strictly prohibited**
* TLS certificates must be:
  * Valid and issued by trusted authorities
  * Properly stored and protected
  * Periodically rotated


## Network Interface

### Scope

The following interfaces are within scope of this control:

* System network interfaces
* Container network interfaces (e.g., Docker)
* REST APIs
* Webhooks / callback endpoints

### Network Access Control Requirements

* All network ports must follow a **default-deny policy**:
  * All ports are **denied by default**
  * Only explicitly required ports and services are **whitelisted**
* Unused or unnecessary services must be **disabled or removed**
* Implement **network segmentation** where applicable:
  * Internal vs external network isolation
  * Container network isolation
* Administrative interfaces must:
  * Not be exposed to public networks
  * Be restricted via VPN, bastion host, or internal network


### Secure Communication Requirements

* All external and inter-service communication must be encrypted (TLS-based)
* Mutual TLS (**mTLS**) should be considered for **service-to-service communication**
* Certificates and keys must be centrally managed (e.g., secret management system)
* Avoid exposing services directly; use:
  * Reverse proxy (e.g., NGINX, Traefik)
  * API Gateway where appropriate


## API Guard

### Scope

* All APIs exposed externally by the system
* All internal APIs between services/modules (including containerized services)


### Input Validation and Threat Protection

* All API requests must undergo **strict validation**, including:
  * Data type, format, length, and range checks
  * Whitelisting of allowed parameters and values

* The system must **reject immediately**:
  * Invalid commands
  * Malformed requests
  * Unexpected or suspicious input patterns

* The system must explicitly protect against:
  * SQL Injection
  * Command Injection
  * Cross-Site Scripting (XSS)
  * Path Traversal
  * Deserialization vulnerabilities


### Authentication and Authorization

* All APIs must enforce **strong authentication**, such as:
  * OAuth 2.0
  * JWT (with signature validation)

* Authorization must:
  * Follow the **principle of least privilege**
  * Enforce **role-based access control (RBAC)** where applicable

* Sensitive endpoints must include:
  * Token expiration and renewal mechanisms
  * Scope/permission validation
  * Protection against token replay

### API Protection Controls

* Implement **rate limiting / throttling** to prevent abuse and DoS attacks
* Apply **IP allowlist/denylist** where appropriate
* Use **API Gateway or WAF (Web Application Firewall)** for:
  * Traffic inspection
  * Attack pattern detection
* All API activities must be:
  * Logged (request, response status, source IP)
  * Monitored for anomalies


### Secure Development and Operations (Recommended)

* Enforce **API schema validation** (e.g., OpenAPI) during development and in CI
* Prevent **hardcoded credentials** (use secret management)
* Perform **security testing**:
  * Static analysis (SAST)
  * Dependency scanning
  * API security testing
 

## Database Security

### Use Stored Procedures

Use **stored procedures** for all database operations instead of embedding raw SQL queries or data access logic directly in application code.  
Stored procedures help to:

* Enforce consistent access control
* Reduce the risk of SQL injection
* Centralize business logic within the database layer
* Improve maintainability and auditability

Direct queries (especially dynamic SQL constructed in code) should be avoided unless absolutely necessary and properly parameterized.

### Password Storage and Encryption

Passwords **must never be stored in plain text**. They must be securely protected using **strong cryptographic hashing algorithms**, such as:

* **bcrypt**
* **Argon2**
* **PBKDF2**

> [!important]  
> Algorithms like **MD5** or **SHA-1 are no longer considered secure** and must not be used for password storage.  

Additional requirements:

* Always use a **unique salt** for each password
* Consider using **adaptive hashing** (configurable workload/cost factor)
* Do not implement your own cryptographic algorithms—use well-established libraries


## Removable Devices

All external interfaces supporting removable devices (e.g., USB ports, SD card slots, external storage interfaces) shall be **disabled or strictly controlled** in systems delivered to customers, in order to mitigate cybersecurity risks and prevent unauthorized access or unintended system modification.

In alignment with **IEC 61508 (functional safety lifecycle and system integrity)** and **ISO 13849 (safety-related parts of control systems)**, the following requirements shall apply:

### Requirements

* **Default State (Deployment)**
  * All removable device interfaces shall be **disabled by default** in production/released systems.
  * Access shall only be permitted through **authorized and controlled service procedures**.

* **Security and Safety Justification**
  * Removable media interfaces shall be treated as **potential hazard injection points**, capable of introducing:
    * Unauthorized software
    * Malware or configuration changes
    * Unvalidated firmware updates
  * Disabling such interfaces supports:
    * Preservation of **system integrity**
    * Prevention of **unauthorized safety function bypass**
    * Compliance with **defense-in-depth principles**

* **Controlled Enablement ([Maintenance Mode](./System-Mode-and-State.md))**
  * If required for diagnostics or maintenance:
    * Interfaces shall only be enabled in a **restricted system Mode** (e.g., *Maintenance Mode*)
    * Access shall require:
      * **Authentication and authorization**
      * **Audit logging**
      * **Physical or procedural control (e.g., service personnel only)**

* **Traceability and Verification**
  * The status (enabled/disabled) of removable interfaces shall be:
    * Defined in the **Safety Requirements Specification (SRS)**
    * Verified during **validation and acceptance testing**
    * Traceable to **hazard analysis and risk assessment** (e.g., FMEA, threat modeling)

* **Failure and Fault Handling**
  * Any unintended activation or misuse of removable interfaces shall:
    * Trigger **diagnostic detection mechanisms**
    * Cause transition to a **safe or risk-reduced state**, if system integrity is compromised



### Compact Architecture Rule

> Removable device interfaces (e.g., USB ports) shall be disabled in all production systems. Any temporary enablement shall be restricted to authorized maintenance modes with proper authentication, monitoring, and traceability, ensuring system integrity and compliance with functional safety requirements.  


## Third-Party Utility Management 

* All third-party utilities used in this project must be thoroughly reviewed and scanned to identify any potential vulnerabilities or backdoor risks. Identified utilities should then be documented in the Software Bill of Materials (SBOM). 
* The SBOM should: 
    * List all dependencies, including both direct and transitive 
    * Record each dependency's version, license, source, and hash 
    
