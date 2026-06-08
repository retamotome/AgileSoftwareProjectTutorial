# Practical System Design Considerations   

> [!note]    
> ![BY NC ND](../../img/Cc-by-nc-sa.png)  
> Practical System Design Considerations © 2018 by Jen Yuan Pan is licensed under [Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.en).  

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

### Memory Dump Strategy

- Perform memory dumps **after processing large datasets in memory**, particularly when the system is under heavy load or showing performance degradation.  
- Dumps provide a **snapshot of the system state**, helping prevent data loss and enabling detailed post‑mortem analysis.  
- They are essential for diagnosing **memory leaks, bottlenecks, and hidden issues** that may not be visible through logs alone.  
- Regularly scheduled or event‑triggered dumps improve **system reliability and troubleshooting efficiency**.  
- Ensure dumps are securely stored and accessible for analysis, while considering **data sensitivity and compliance requirements**.  

## Build a Quality-Assurable System 

### System Mode and State
Please refer to the [System Mode and State](./System-Mode-and-State.md)  for details.  

## Failure Tolerance Strategy 

### Redundancy and Failover

Provide **high availability** and **service continuity** by ensuring critical components can automatically recover or switch to backup systems when failures occur.  

* Deploy **hot standby / failover systems** for critical services
* Use **replication mechanisms** (sync or async based on RPO requirements)
* Ensure failover nodes have **independent power sources**


### Packaging Modules in Docker Images   

Most systems do not implement full redundancy due to cost and complexity. Instead, a **containerized architecture** is used, where each module runs as an independent Docker container.

* Failures are **isolated per module**
* Only the affected container needs to be **restarted**

### A/B Update Mechanism   

To ensure safe updates and prevent system corruption, the A/B Update strategy uses two identical volumes managed by an Update Manager. New packages are deployed to volume B, and the system reboots from volume B: 
 * If booting from volume B fails, the system automatically reverts to volume A, erases volume B, and restores it from volume A. 
 * If booting from volume B succeeds, volume A is erased and synchronized with the contents of volume B.   

## Load Balancing Strategy

### CPU Affinity

To prevent heavy processes from overwhelming shared CPU resources and starving other critical services, **CPU affinity should be carefully designed and enforced**.

* Assign CPU cores explicitly to high-load or real-time processes to ensure predictable performance.
* Isolate critical services from non-critical workloads to avoid resource contention.
* Avoid unrestricted process scheduling, which may lead to CPU saturation, increased latency, or system stalls.
* Consider NUMA awareness (if applicable) to further optimize performance on multi-socket systems.

Proper CPU affinity planning helps achieve **deterministic execution**, improved system stability, and better overall resource utilization.

### NIC Binding (Network Interface Bonding / Teaming)

NIC binding combines multiple network interfaces to improve both **bandwidth availability** and **fault tolerance**.

* **Increased bandwidth**: Traffic can be distributed across multiple NICs, reducing bottlenecks.
* **High availability**: If a single network interface fails, traffic is automatically redirected to remaining interfaces.
* **Improved reliability**: Reduces the risk of connection loss due to single point of failure.

Depending on the system design, NIC binding can be configured in modes such as:

* Active-backup (failover-focused)
* Load balancing (throughput-focused)
* LACP (Link Aggregation Control Protocol, IEEE 802.3ad) for dynamic link aggregation

This ensures **resilient and scalable network performance**, especially in high-throughput or mission-critical environments.

## Power Failure Handling

To reduce data loss and system risks during power failures, implement a layered protection strategy.

**Power Protection**: Use UPS and redundant power supplies to provide buffer time.  
**Graceful Shutdown**: Detect power loss and trigger controlled shutdown (flush data, stop services, move hardware to safe state).  
**Data Protection**: Use journaling file systems and ensure critical data is written (fsync/checkpoints).  
**Checkpoint & Recovery**: Periodically save system state and support safe restart from last checkpoint.  
**Transaction Safety**: Use atomic operations and retry mechanisms to prevent partial updates.  
**Startup Recovery**: Detect abnormal shutdown and resume or roll back safely.  


## Data Processing Strategy  
Please refer to the [Data Processing Strategy](./Data-Processing-Strategy.md) for details.

## Security Consideration 
Please refer to the [Security Consideration](./Security-Consideration.md) for details.  
