# Configure and manage storage accounts

## Modules

Below are links to the relevant modules for each listed learning objective. Modules that cover multiple objectives will be listed multiple times.

- Create and configure storage accounts
  - [Configure storage accounts](https://learn.microsoft.com/en-us/training/modules/configure-storage-accounts/)
- Configure Azure Storage redundancy
  - [Azure Storage redundancy](https://learn.microsoft.com/en-us/azure/storage/common/storage-redundancy) (not part of AZ-104 course material)
- Configure object replication
  - [Configure Azure Blob Storage](https://learn.microsoft.com/en-us/training/modules/configure-blob-storage/)
- Configure storage account encryption
  - [Configure Azure Storage security](https://learn.microsoft.com/en-us/training/modules/configure-storage-security/)
- Manage data by using Azure Storage Explorer and AzCopy
  - [Configure Azure Storage with tools](https://learn.microsoft.com/en-us/training/modules/configure-storage-tools/)

## Relevant Exercises

- [Create a storage account using the Azure portal](https://learn.microsoft.com/en-us/training/modules/create-azure-storage-account/5-exercise-create-a-storage-account)
- [Connect Azure Storage Explorer to a storage account](https://learn.microsoft.com/en-us/training/modules/upload-download-and-manage-data-with-azure-storage-explorer/3-exercise-connect-storage-account)
- [Connect Azure Storage Explorer to Azure Data Lake Storage](https://learn.microsoft.com/en-us/training/modules/upload-download-and-manage-data-with-azure-storage-explorer/3-exercise-connect-storage-account)

## Labs

## Summary/Notes

### General

- **Azure Storage:** Microsoft's cloud storage offering for modern data storage scenarios. Provides a massively scalable object store, a file system service for the cloud, a messaging store for reliable messaging, and a NoSQL store.
  - Used by IaaS VMs and PaaS cloud services.
- Support for 3 types of data:
  1. Virtual Machine Data: includes disks and files. Disks are stored as persistent block blobs, and files are fully managed file shares.
  2. Unstructured Data: non-relational data can be stored with Blob Storage and Data Lake Storage.
     - Blob Storage is highly scalable and REST-based.
     - Data Lake is Hadoop Distributed File System (HDFS) as a service.
  3. Structured Data: relational data with a shared schema; often contained in a database table with rows, columns, and keys.
     - Options include Azure Table Storage (an auto-scaling NoSQL store), Azure Cosmos DB, and Azure SQL DB.
- Key features:
  - Storage is durable, highly available, and redundant.
  - All data in Azure Storage is encrypted.
  - Designed to be massively scalable.
  - Data is accessible anywhere over HTTP(S). SDKs are available for .NET, Java, Node.js, Python, PHP, Ruby, Go, etc.

### Create and Configure Storage Accounts

- Storage Tiers: storage accounts have two tiers, Standard and Premium. The tier is chosen upon storage account creation and cannot be changed.
  - Standard: backed by magnetic HDDs. This tier is the lowest cost per GB, suitable for apps that require bulk storage, or for storing infrequently accessed data.
  - Premium: backed by SSDs offering low-latency performance. Suitable for VM disks with I/O intensive workloads.
- Storage Services:
  - **Azure Blob Storage:** Massively scalable large object store for text and binary data.
    - Ideal for:
      - Serving images or documents directly to a browser.
      - Storing files for distributed access.
      - Streaming videos and audio.
      - Storing data for backup and restore, disaster recovery and archiving.
      - Storing data for analysis.
    - Files can be accessed via blob URLs, REST API, Azure PowerShell, Azure CLI, or client libraries.
  - **Azure File Storage:** Managed, highly available file shares for cloud or on-prem deployments.
    - Ideal for:
      - Migrating apps that share data to the cloud: cloud file shares can be mounted to the same drive letter as the on-prem file shares they are meant to replace.
      - Storing config files, tools, and utilities to access from multiple VMs for organization-wide use.
      - Storing diagnostics, logs, metrics, and crash dumps for later analysis.
    - Storage account credentials are used for access management.
  - **Azure Queue Storage:** Messaging storage for reliable messaging between application components.
    - Can store and retrieve messages ip to 64KB in size.
    - Queues can store millions of messages.
  - **Azure Table Storage:** Now part of Azure Cosmos DB, a NoSQL store for schemaless storage of structured (relational) data.
  - **Azure Cosmos DB:** Fully managed NoSQL database service for modern app development.
    - Fully managed service-- no database administration required.
    - Serverless and automatic scaling options available.
    - Existing Table Storage service and Cosmos DB API offer throughput-optimized tables, global distribution, and automatic secondary instances.
    - Ideal for structured or relational data.
- Storage account types:
  - Standard General-Purpose V2: Blob storage, including Data Lake storage, Queue Storage, Table Storage, and Azure Files Storage all supported. Most commonly used storage account type.
  - Premium Block Blob: Blob Storage only (includes Data Lake). Premium tier account for block and append blobs. Used for apps with high transaction rates and a requirement for low latency.
  - Premium File Shares: File share only. Recommended for enterprise-scale or high-performance apps and scenarios which require SMB and NFS file share support.
  - Premium Page Blob: Support for page blobs only. Ideal for storing index-based and sparse data structures like OSs, VM data disks (Azure VM disks backed by page blobs), and databases.

### Configure Azure Storage Redundancy

- All data in storage accounts in replicated for protection.
- Different flavours of replication:
  1. Locally Redundant Storage (LRS): data is replicated 3 times within the same datacenter.
     - Lowest-cost replication option.
     - Risk of losing data in datacenter-wide disaster
     - Ideal for:
       - Data that can be easily reconstructed if lost.
       - Data the changes frequently such that storing it is not essential.
       - Data that is restricted to a single country or region by data governance.
     - Ensures data can still be accessed if a node becomes unavailable.
  2. Zone Redundant Storage (ZRS): synchronously replicates data across three storage clusters in the same region.
     - Each storage cluster is physically separated with its own utility and network, and in a separate availability zone.
     - Not available in all regions.
     - Ensures data can still be accessed if a zone becomes unavailable.
     - Changing to ZRS requires physical movement of data from single storage stamp to multiple stamps.
  3. Geo-Redundant Storage (GRS): replicates data to a secondary region.
     - Higher levelof durability, even during a regional outage (at least 16 9's).
     - Data replicated using LRS in both regions.
     - Two flavours:
       - GRS: data available as read-only from secondary region only if Microsoft initiates a failover from the primary region to the secondary.
       - RA-GRS: (read-access GRS) option to read from the secondary region regardless of whether a failover has been initiated.
  4. Geo-Zone Redundant Storage (GZRS): combines GRS and ZRS, data is replicated into 3 availability zones in primary and secondary region.
     - Continue to read and write data during a zone-wide outage; durability during region-wide outage.
     - 16 9's durability over a year.
     - RA-GZRS also available.

### Configure Object Replication

### Configure Storage Account Encryption

### Manage Data by Using Azure Storage Explorer and AzCopy
