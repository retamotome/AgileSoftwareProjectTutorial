# Data Processing Strategy

> [!note]    
> ![BY NC ND](../../img/Cc-by-nc-sa.png)  
> Data Processing Strategy © 2018 by Jen Yuan Pan is licensed under [Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.en).  

## Data Format
For configuration files, adopt **human-readable formats** such as JSON or YAML to simplify maintenance and reduce the need for complex queries.   
When handling data that requires frequent searching, filtering, or analytical processing, leverage a **database system** to ensure optimal performance, scalability, and efficiency.  

## Using External Storage

Always perform a **full format** when initializing external storage (e.g., SD cards, HDDs, removable media).

* Full format scans and **identifies bad sectors**, marking them unusable.  
* Ensures **data integrity** and more reliable long-term operation.  

> [!important]  
> **Avoid** using **shared folders or network-shared storage** for critical data:
> * Data transfer is **not encrypted by default** (e.g., SMB/NFS), unless explicitly configured.  
> * Exposes risk of **data interception, leakage, or unauthorized access**.  


## Ensuring Reliable Data Sync  

- **Closing a file (`close()`) is not enough**: It only flushes user‑space buffers to the OS; data may still remain in the kernel’s page cache.  
- **Explicit flush required**: Use `fsync(fd)` or `fdatasync(fd)` on Linux/Unix, and `FlushFileBuffers(handle)` on Windows, to force both data and metadata to stable storage.  
- **Performance trade‑off**: Frequent sync calls can reduce throughput, so durability must be balanced with efficiency.  
- **Structured persistence techniques**: Write‑ahead logging (WAL), checkpointing, and replication provide stronger guarantees of reliability and recovery compared to relying solely on sync calls.  
- **Best practice workflow**: `write → fsync/fdatasync → close` ensures data is truly committed to disk and minimizes risk of loss during crashes or power failures.  


## Data Transmission  

To ensure **data integrity, availability, and confidentiality** during transfer, it is essential to use secure protocols (e.g., **HTTPS, SFTP, TLS**) combined with strong encryption. These measures protect against tampering, interception, and unauthorized access.  

**Built‑in Resume Capability**  
Built‑in resume capability means a protocol or system can **natively continue interrupted transfers** from the last completed point without external tools. Examples include:  
- **FTP** (via the `REST` command)  
- **SFTP** (client‑side offset resume)  
- **BitTorrent** (chunked transfer with checksums)  
- **Cloud storage APIs** (e.g., Amazon S3 multipart uploads, Google Cloud Storage resumable uploads)  

This feature is critical for large data or unstable networks, ensuring efficiency and saving bandwidth.  

**Paging / Chunking**  
For large data transfers, **paging or chunking** techniques improve reliability and memory management. Data is broken into smaller segments, transferred sequentially, and reassembled at the destination. This approach:  
- Simplifies error handling and retries (only failed chunks are resent).  
- Prevents excessive memory usage.  
- Is widely adopted in **REST APIs** and cloud services for handling big datasets.  

**Limitations**  
- Not all protocols support resume (e.g., **SCP, WebDAV**).  
- Resume may fail if the source data changes mid‑transfer or if metadata (timestamps, permissions) is not preserved.  
- Peer or server availability is required (especially in BitTorrent).  

