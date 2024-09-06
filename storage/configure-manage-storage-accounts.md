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
  - Storage is durable, and can be configured to be highly available and redundant.
  - All data in Azure Storage is encrypted.
  - Designed to be massively scalable.
  - Data is accessible anywhere over HTTP(S). SDKs are available for .NET, Java, Node.js, Python, PHP, Ruby, Go, etc.
- Pricing factors:
  - Performance tier: storage tier determines the cost per GB to store data, decreases with cooler tiers.
  - Data access cost: costs to access data increase with cooler tiers; Cool and Archive tiers are billed per-GB for access.
  - Transaction costs: per-transaction charge for all tiers. Cost increases for cooler tiers.
  - Geo-replication: per-GB charge for data transfer in GRS, RA-GRS, GZRS, RA-GZRS configurations.
  - Outbound data transfer cost: data transferred out of a region is billed for bandwidth usage on a per-GB basis.
  - Changes to storage tier: when moving data from Cool to Hot, a charge equivalent to reading all data in the account is incurred. When moving from Hot to Cool, there is a charge equivalent to writing all data into the Cool tier (GPv2 only).

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
  - **Azure Table Storage:** Now part of Azure Cosmos DB, a NoSQL store for schemaless storage of structured data.
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
  1. Locally Redundant Storage (LRS): default replication method; data is replicated 3 times within the same datacenter.
     - Lowest-cost replication option.
     - Risk of losing data in datacenter-wide disaster, but ensures data can still be accessed if a node becomes unavailable.
     - Ideal for:
       - Data that can be easily reconstructed if lost.
       - Data the changes frequently such that storing it is not essential.
       - Data that is restricted to a single country or region by data governance.
  2. Zone Redundant Storage (ZRS): synchronously replicates data across three storage clusters in the same region.
     - Each storage cluster is physically separated with its own utility and network, and in a separate availability zone.
     - Not available in all regions.
     - Ensures data can still be accessed if a zone becomes unavailable.
     - Changing to ZRS requires physical movement of data from single storage stamp to multiple stamps (incurs cost).
  3. Geo-Redundant Storage (GRS): replicates data to a secondary region. In the both thte primary and secondary regions, data is replicated with LRS.
     - Higher level of durability, even during a regional outage (at least 16 9's).
     - Two flavours:
       - GRS: data available as read-only from secondary region only if Microsoft initiates a failover from the primary region to the secondary.
       - RA-GRS: (read-access GRS) option to read from the secondary region regardless of whether a failover has been initiated.
  4. Geo-Zone Redundant Storage (GZRS): combines GRS and ZRS, data is replicated into 3 availability zones (ZRS) in primary region, and with LRS secondary region.
     - Continue to read and write data during a zone-wide outage; durability during region-wide outage.
     - 16 9's durability over a year.
     - RA-GZRS also available.
- Data is always replicated 3 times in the primary region, either via LRS or ZRS.
- ZRS in the primary region and replication to a secondary region is recommended for high availability applications.
- Redundancy has three levels of configuration:
  1. How data is replicated within the primary region: LRS or ZRS
  2. Whether data is replicated to a secondary region: LRS or GRS, ZRS or GZRS
  3. Whether there is read access to the secondary region when geo-redundancy is used: GRS or RA-GRS, GZRS or RA-GZRS.
- Can change replication settings in 3 ways:
  1. Azure portal/Azure CLI: to add or remove geo-replication or read access to the secondary region.
     - In portal, setting is under **Data Management > Redundancy**.
     - To use the Azure CLI, use `az storage account update` with the `--sku <sku>` option set.
  2. Perform a conversion: to add or remove zone-redundancy.
     - There is no data loss or downtime in a conversion.
     - Can be customer-initiated (recommended), or support-requested.
     - Customer initiated conversions can take up to 72 hours to begin after initiation, and there is no SLA on completion time. Only available in the Azure portal, and not available in all regions; use the same settings blade as above to initiate.
  3. Perform a manual migration: to ensure change is completed by a specific deadline, or when other methods aren't supported.
  - More flexible than a conversion. Use when conversion has a deadline, or when it's not supported.
  - Manual migration is required when:
    - Moving a storage account to a different region.
    - For block blob accounts.
    - There is data in the archive tier, and rehydrating it is not desired.
  - This can result in downtime.
  - Manual migrations are performed by copying data to a new storage account with AzCopy or Azure Storage client libraries.
- Some conversions may be a multi-step process:

  |     Switching     |                     to LRS                      |                          to GRS/RA-GRS                          |                     to ZRS                      |                            to GZRS/RA-GZRS                            |
  | :---------------: | :---------------------------------------------: | :-------------------------------------------------------------: | :---------------------------------------------: | :-------------------------------------------------------------------: |
  |     from LRS      |                        -                        |                           Portal/CLI                            |              Perform a Conversion               | Switch to GRS/RA-GRS first, then perform a conversion to GZRS/RA-GZRS |
  |  from GRS/RA-GRS  |                   Portal/CLI                    |                                -                                | Switch to LRS, then perform a conversion to ZRS |                         Perform a conversion                          |
  |     from ZRS      |              Perform a conversion               | Switch to GZRS/RA-GZRS, then perform a conversion to GRS/RA-GRS |                        -                        |                              Portal/CLI                               |
  | from GZRS/RA-GZRS | Switch to ZRS, then perform a conversion to LRS |                      Perform a conversion                       |                   Portal/CLI                    |                                   -                                   |

### Configure Object Replication

- Replication copies blobs asynchronously (including metadata and versions) from one container to another.
- Supports only block blobs in General Purpose v2 or Premium Blob storage accounts.
- Object replication requires versioning to be enabled on source and destination accounts.
- Blob snapshots are not replicated from source account to destination account.
- Blobs in Hot and Cool tiers are supported.
- Configuration: create replication policy specifying source and destination storage accounts.
  - Replication policy contains one or more rules specifying source and destination container and blobs to replicate.
  - Azure portal: replication policy only needs to be created on the source account. The replication policy is automatically handled on the destination account.
    - Found under **Data Management > Object Replication > Set Up Replication Rules**
    - Can configure up to 10 container pairs in portal. Can configure up to 1000 per policy when using JSON.
    - Can configure filters and scope.
  - Azure CLI: (assumes versioning enabled and containers exist in source and destination accounts).
    - Create policy on destination account with `az storage account or-policy create` and provide source and destination accounts/containers, and rules
    - Add rules to policy with `az storage account or-policy rule add`
    - Add policy to source account with
      ```bash
      az storage account or-policy show \
        --resource-group <resource-group> \
        --account-name <dest-storage-account> \
        --policy-id <policy-id> |
      az storage account or-policy create --resource-group <resource-group> \
        --account-name <source-storage-account> \
        --policy "@-"
      ```
- Common use cases:
  - Reduce latency on read requests by replicating data to a storage account closer to users' physical locations.
  - Improve efficiency of compute workloads by allowing them to process the same sets of data in different regions.
  - Optimize for data distribution; do processing in one location and replicate results to multiple regions.
  - Optimize cost benefits by using lifecycle management to archive blobs after replication.

### Configure Storage Account Encryption

- All data written to storage accounts is automatically encrypted before it's persisted to Storage.
- Data is automatically decrypted before retrieval.
- Storage encryption, encryption at rest, decryption, and key management are all transparent to users.
- Data is encrypted through 256-bit AES; encryption is enabled for all storage accounts and cannot be disabled.
- Can choose the encryption type: Customer-managed or Microsoft-managed keys.
  - Customer-managed keys: provide more control and flexibility than the default Microsoft-managed keys.
    - Can use a Key Vault to manage encryption keys and optionally generate the keys using the Key Vault APIs.
    - Create, disable, audit, rotate, and define access controls for keys.
    - Using customer-managed keys with Azure Storage:
      - The key vault and storage account must be in the same region (although they can be in different subscriptions).
      - Specify the key with a URI, or select from an existing key vault.

### Manage Data by Using Azure Storage Explorer and AzCopy

- Several upload tools available for Azure Storage:
  1. **Azure Storage Explorer:** standalone application for working with Azure Storage on Windows, MacOS, and Linux. Allows management of all storage content across multiple accounts and subscriptions.
     - Requires management and data layer permissions for full access.
     - Can be used to manage local storage with Azure Storage Emulator.
     - To attach to an external storage account, need account name and account key.
  1. **AzCopy:** CLI for Windows and Linux that can copy data to and from Blob storage, and across containers and storage accounts. Built on Azure DML.
     - As of version 10, has a redesigned CLI and architecture, making it high performance and reliable.
     - Uses:
       - Copy data between storage accounts, or between storage accounts and on-premises storage.
       - List or remove files or blobs in a given path.
       - Ideal for incremental copy scenarios.
     - Every AzCopy instance creates a job order with related log files to make reviewing, restarting, resuming easy. Automatically retries a transfer upon failure.
     - With Blob storage, can transfer an entire storage account to another using a `PUT`. No need to transfer to the client in this process.
     - Support for DataLake Gen2 APIs.
     - Built into Azure Storage Explorer (Storage Explorer uses AzCopy for all transfer operations) and supported on Windows, Linux, and MacOS.
     - Basic CLI syntax: `azcopy <job> [args]`
     - Authentication options:
       1. Azure AD sign-in: applicable for Blob and Data Lake storage, when the identity being used has at least the Storage Blob Data Contributor Role. Allows user to circumvent the SAS in each command.
       2. SAS token: the SAS must be provided as an argument for every command (Blob and Files).
  1. **Azure Storage Data Movement Library (DML):**.NET library for moving between storage services.
  1. **Azure Data Factory:** Copy data to and from Blob storage.
  1. **blobfuse:** virtual file system driver for Blob storage. Allows access to existing block blob data through a Linux filesystem.
  1. **Azure Data Box Disk:** service for transferring on-prem data to blob storage when uploading over the wire is unrealistic. SSDs can be requested from Microsoft, data can be copied onto them and then sent back to Microsoft datacenter for upload.
  1. **Azure Import/Export:** service to export large amounts of data from your storage account to hard drives that are then shipped to you.
     - Can be used in reverse to get data from the cloud to on-premises.
     - Can create an import or export job in the portal or via the Import/Export REST API.
     - Useful when uploading/downloading over the network is too slow or prohibitively expensive.
     - To create an import job:
       1. Create the storage account in Azure.
       2. Prep disks and machine(s) housing data for the data copy.
       3. Install the `WAImportExport` tool on the disks.
       4. Run the tool (encrypts disks, generates journal files, copies data).
       5. Create the Azure Import job.
       6. Ship the disks (update job to include tracking number for shipment).
       7. When staff receive disks at the datacenter, they will copy the data over and ship the disks back.
     - To create an export job:
       1. Identify data for export and determine disk requirements.
       2. Create the export job in the portal.
       3. Ship the disks to the Azure datacenter (update the job to include tracking number for shipment).
       4. When staff receive the disks, they will copy the data, encrypt it, and send the disks back.
       5. Decrypt the data using the the BitLocker keys stored in the storage account.
     - **WAImportExport Tool:** tool used by the Azure Import/Export service to prepare devices for importing data, and repair any corrupted data after a transfer. Handles the data copy, data repair, volume encryption, and journal file creation. Must be used with 64-bit Windows OS and SATA II or SATA III HDDs/SSDs.
