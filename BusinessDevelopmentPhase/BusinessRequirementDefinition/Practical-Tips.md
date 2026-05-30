# Business Requirement Definition Practical Tips   

> [!note]  
> ![BY NC ND](../../img/Cc-by-nc-sa.png)  
> Business Requirement Definition Practical Tips © 2026 by Jen Yuan Pan is licensed under [Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.en).  

## Overview 

This section provides a concise overview of key reliability strategies and general considerations essential during the Requirement Analysis phase of software engineering and project management, along with practical tips based on my real-world experience.   
Companion video: [Lesson 02: Business Requirement Definition 商業需求定義](https://github.com/retamotome/SelfDirectedTraining/blob/main/AgileSoftwareEngineering/2025-12-08_BusinessReqDef/README.md).  
For guidance on writing requirements, see [Writing Requirements Effectively](./Writing-Requirements-Effectively.md). 

## System Architecture 

In software engineering, a system is often broadly divided into two parts: User Space (or Application Space) and Kernel Space.   
When designing in **User Space**, factors like **variability** and **scalability** should be considered from the user's perspective. In **Kernel Space**, **stability** is the top priority, as it is the most critical requirement for the kernel.   
There is **no** universal system architecture that fits all scenarios. In reality, **system architecture is influenced by user behavior, hardware constraints, physical environment, contractual limitations, compliance policies, and more**. 

## Build a Trouble-Shootable System 

Troubleshooting is never easy, often because "ease of troubleshooting" is rarely considered during the requirement analysis phase. Software engineering is not just about processes, procedures, management, and quality assurance—it also provides mechanisms and methods to build systems that are easier to troubleshoot, especially when issues occur and logs are missing.   
 

### Audit Log 

It is a common misconception that logs are only for developer debugging. In reality, **the primary purpose of an audit log is to help users quickly troubleshoot when errors occur**—yes, users, not just developers.   
When logs are clear and user-friendly, they also make it much easier for developers to debug issues. As a result, end users are less likely to complain or require developer intervention every time a server error occurs.   

### Resource Usage 

Do **not** assume logs will always be saved when errors occur—especially during critical failures such as crashes, power outages, segmentation faults, or sudden reboots. 
Fortunately, Linux provides several mechanisms to capture system state (e.g., kernel dumps, memory dumps, process dumps) when a crash or segfault happens and there is no time to write logs.   
Always ensure that **at least 30% of system resources** (CPU, memory, disk, etc.) **remain available**, and **implement timely dumps** when sudden failures occur.   
Note that memory dumps do **not** guarantee data is written to disk. Many enterprise-level high-performance drives use DRAM cache to speed up transfers, so data loss can occur if cached data is lost during a power failure.   

## Build a Quality-Assurable System 

### System Mode and State 

Understanding and clearly defining **System Modes** and **System States** is essential for robust requirement analysis. This ensures the system behaves predictably under all circumstances, supports troubleshooting, and meets both operational and safety requirements. 

> [!note] 
> + A well-designed system should define at least three distinct Modes, each with its own set of States. (A system with only a single state is generally considered a poor design in software engineering.) 
> + Both Modes and States should clearly **specify their respective capabilities and restrictions**. This approach is fundamental to building **quality-assurable** systems. 
> + Properly defined Modes and States **control what the system is allowed to do**, rather than merely reflecting its current activity. 


#### System Modes 

System modes describe the overall operational context or intent of the system. Each mode has distinct priorities, restrictions, and available features. Common modes include: 

- **Running Mode (Auto Operation)** 
    - System delivers normal services to users. 
    - Prioritizes stability, safety, and performance. 
    - Minimal logging (only errors or critical events). 
    - Debugging tools and test hooks are disabled. 
    - Optimized for high availability and monitoring. 
    - No debug overhead for maximum efficiency. 

- **Manual Mode (Operator Control)**   
    - Used primarily for diagnostics, troubleshooting, and validation.   
    - Lower performance is acceptable in favor of visibility and control.   
    - Enables additional logging, diagnostics, and sometimes step execution.   
    - May expose features not available in production (e.g., breakpoints, verbose logs).   
    - Typical differences between development and production environments:   

        | Feature / Item           | Development         | Production (Troubleshooting) | 
        |-------------------------|---------------------|------------------------------| 
        | Mode Name               | Debug Mode          | Troubleshooting Mode         | 
        | Bug Reproduction        | Off-site            | On-site                      | 
        | Logic Verification      | Off-site            | On-site                      | 
        | Logging Level           | Debug               | Trace                        | 
        | Breakpoints/Step Exec   | Yes                 | No                           | 
        | Diagnostics             | Manual              | Utilities with product       | 


> [!note]    
> Diagnostic utilities should be delivered with the product for field troubleshooting.    
> Debug features are typically disabled in production builds to improve performance and security. 

- **Maintenance Mode** 

    - Used for updates, patches, and configuration changes. 
    - Restricts normal user access; only administrators can interact. 
    - System may be partially or fully unavailable. 
    - Supports data migration, upgrade scripts, and controlled downtime. 
    - Sometimes called Service Mode or Admin Mode. 

- **Simulation Mode** 

    - Allows the system to run without real hardware (e.g., for testing or training). 
    - Useful for development, validation, and operator training. 

- **Safe Mode** 

    - Activated to protect the system when faults or unsafe conditions are detected. 
    - Limits system functionality to prevent damage or hazards. 
    - Examples: 
        - Stop robot motion 
        - Disable unsafe operations 
        - Switch to minimal, safe functionality 

#### System States 

System states represent the current condition or phase of the system within a given mode. States often transition in response to events, commands, or errors. Typical states include: 

- **Startup / Initialization** 

    - System boots and prepares for operation. 
    - Tasks include loading configuration, initializing hardware/connections, and performing self-checks. 

 

- **Standby / Idle** 

    - System is powered but not actively processing. 
    - Waiting for commands or events; may enter low-power state. 

- **Running (Auto Operation)** 
- 
    - System is actively performing its main function. 
    - Equivalent to Running Mode. 

- **Degraded / Fallback** 

    - System continues operation with reduced capability due to partial failure. 
    - Examples: 
        - One sensor fails → system still runs with limited features 
        - Reduced performance, but not a complete stop 

- **Emergency** 

    - Immediate response to critical faults or hazards. 
    - Examples: 
        - Emergency stop (E-stop) 
        - Alarm-triggered state 

- **Shutdown** 

    - System gracefully stops operation. 
    - Tasks include closing connections, saving state, and preventing data loss. 

> [!tip]  
> When defining requirements, always specify which modes and states are relevant, how transitions occur, and what restrictions or features apply in each. This clarity helps prevent ambiguity and ensures the system is reliable, safe, and maintainable. 

## Failure Tolerance Strategy 

**Packaging Modules in Docker Images**   

Due to the Cortex architecture’s limited resources compared to traditional PCs, the system does not support full redundancy for failure recovery. Instead, each module is containerized as an independent Docker image. If a module fails, only the affected Docker instance needs to be restarted, minimizing system disruption.   

**A/B Update Mechanism**   

To ensure safe updates and prevent system corruption, the A/B Update strategy uses two identical volumes managed by an Update Manager. New packages are deployed to volume B, and the system reboots from volume B: 
 * If booting from volume B fails, the system automatically reverts to volume A, erases volume B, and restores it from volume A. 
 * If booting from volume B succeeds, volume A is erased and synchronized with the contents of volume B.   

## Security Consideration 

* All external connections must be secured with encryption. 

### Network Interface 

* **Scope:** 

    * System network interfaces 
    * Docker network interfaces 
    * REST API 
    * WebHook 

* All ports are **denied** by default; only explicitly required ports are whitelisted to minimize exposure. 

 

### API Guard 

* **Scope:** 

    * All APIs provided by the system for external access 
    * All APIs exposed by modules within containers 

* Reject any invalid commands, parameters, or patterns that could introduce security risks (e.g., SQL injection). 
 

### Third-Party Utility Management 

* All third-party utilities used in this project must be thoroughly reviewed and scanned to identify any potential vulnerabilities or backdoor risks. Identified utilities should then be documented in the Software Bill of Materials (SBOM). 
* The SBOM should: 
    * List all dependencies, including both direct and transitive 
    * Record each dependency's version, license, source, and hash 