# Configure Azure Files and Azure Blob Storage

## Modules

Below are links to the relevant modules for each listed learning objective. Modules that cover multiple objectives will be listed multiple times.

- Create and configure a file share in Azure Storage
  - [Configure Azure Files and Azure File Sync](https://learn.microsoft.com/en-us/training/modules/configure-azure-files-file-sync/)
- Create and configure a container in Blob Storage
  - [Configure Azure Blob Storage](https://learn.microsoft.com/en-us/training/modules/configure-blob-storage/)
- Configure storage tiers
  - [Configure Azure Blob Storage](https://learn.microsoft.com/en-us/training/modules/configure-blob-storage/)
- Configure snapshots and soft delete for Azure Files
  - [Configure Azure Files and Azure File Sync](https://learn.microsoft.com/en-us/training/modules/configure-azure-files-file-sync/)
- Configure blob lifecycle management
  - [Configure Azure Blob Storage](https://learn.microsoft.com/en-us/training/modules/configure-blob-storage/)
- Configure blob versioning
  - [Enable and manage blob versioning](https://learn.microsoft.com/en-us/azure/storage/blobs/versioning-enable?tabs=portal) (not part of AZ-104 course material)
  - [Blob versioning](https://learn.microsoft.com/en-us/azure/storage/blobs/versioning-overview) (not part of AZ-104 course material)

## Relevant Exercises

## Labs

## Summary/Notes

### General

- Common use cases for Blob storage:
  - Serving documents or images directly to a browser.
  - Storing files for distributed access.
  - Streaming audio or video.
  - Storing backups, data for disaster recovery, archives.
  - Storing data for later analysis by other services.
- Blob types:
  - **Block Blob:** blocks of data assembled to make a blob. Most commonly used blob type, ideal for text and binary data.
  - **Append Blob:** bocks of data optimized for append operations. Useful for loggin scenarios.
  - **Page Blob:** can be up to 8TB in size, optimized for frequent read/write operations. Used by Azure VMs for operating system and data disks.
  - The default type for new blobs is block blob. Blob type can not be changed after creation.

### Create and Configure a File Share in Azure Storage

- Azure Files offers shared storage using SMB (Server Message Block) protocol.
  - VMs and other cloud services can share data by mounting file shares (also allows on-prem access).
  - File shares are fully managed and can be mounted by Windows, Linux, and MacOS machines.
- Use cases:
  - Replace/supplement traditional on-prem file servers and NAS devices.
  - Cloud lift and shift.
  - Data accessibility: access data from any OS, anywhere in the world.
  - Store shared application settings (ex. config, tools, utilities) in a central location for consumption by machines, or use as a shared location to write logs/diagnostic data.
  - Replicate Azure File Shares to Windows Servers with Azure File Sync (servers can be on-prem or in cloud) for increased performance and distributed caching.
- Comparison with Blob Storage:
  1. Files provides SMB and NFS protocols, REST API, and client libraries. Blobs provide a REST API and client libraries to allow unstructured data access on a massive scale.
  2. Files stored in Azure Files are a true directory namespace, and data is accessed through file shares. Blobs in Blob Storage are a flat namespace and accessed through a contianer. Page blobs in Disks are 512-byte pages and exclusive to a single VM.
  3. Azure Files is ideal for lift & shift, and good for storage of shared tools. Blob storage is ideal for streaming and random access. Page blobs in Disks are ideal for random read & write operations & storing relational data for OS and data disks used by VMs.
- Azure Files uses SMB, which communicates over TCP port 445; that port must be open and not blocked by firewall in order for communication with file share to be successful.
- Can (and should) limit communication to secure connections only by using the "Secure transfer required" setting.
- Connecting to Azure Files:
  - Can connect file share to Windows or Windows Server machine in the Azure Portal.
  - Can connect to Linux machine using CIFS kernel client. Mount on-demand with the `mount` command, or persistently with an entry in `/etc/fstab`.
- **Azure File Sync:** Tool which enables the caching of several Azure Files shares on an on-prem Windows Server or cloud VM.
  - File Sync allows users to centralize their file shares in Azure Files while keeping the flexibility, performance, and compatibility of an on-prem share.
  - Transforms Windows Server into a quick cache of Azure Files shares.
  - Any protocol (SMB, NFS, FTPS) can be used to access the local copy of the share.
  - Azure Backup will backup synced files.
- Azure File Sync has a Cloud Tiering (optional) feature:
  - Frequently accessed files are cached locally while all other are tiered to Azure based on policy settings.
    - When a file is tiered, Azure replaces the file locally with a pointer (reparse point; represents a URL to the file in Azure Files).
    - When the tiered file is opened, Azure File Sync recalls the data from Azure Files
    - Cloud tiered files are displayed with a greyed icon with the offline "O" to show that they are only in Azure Files.
    - Useful for archiving files.
- File Sync components:
  - Storage Sync Service: top level resource for Azure File Sync (ie. a peer of the storage account containing Azure Files).
    - Sync Service forms relationship with multiple storage accounts using "sync groups".
    - There can be more than one sync service deployed per subscription.
  - Sync Group: defines the sync topology for a set of files. Endpoints within a sync group are kept in sync with each other.
  - Registered Server: represents a trust relationship between the on-prem server acting as the cache and the File Sync Service.
  - Azure File Sync Agent: downloaded agent that enables a Windows Server machine to be synced with an Azure Files share.
    - Composed of `FileSyncSvc.exe` and `StorageSync.sys`. Fomer is the background service responsible for monitoring changes and initiating syncs. Latter is the file system filter responsible for managing cloud tiering (if enabled).
    - Associated PowerShell cmdlets to interact with the Sync resource provider
  - Server Endpoint: specific location (folder or volume) on the registered server. Can have multiple endpoints on the same volume as long as namespaces are unique.
  - Cloud Endpoint: Azure Files share that's part of a sync group.
    - Each Files share can be a member of a single cloud endpoint and sync group only.
    - When adding a Files share to a cloud endpoint, existing files in the share are merged with files on other endpoints in the sync group.
- File Sync deployment steps:
  1. Deploy the File Sync Service (portal/CLI/PowerShell/ARM template).
  2. Prepare on-prem or cloud server for sync.
  3. Install the File Sync agent on the server.
  4. Register each server with Azure File Sync. Registration opens on server when the agent installation finishes. A server can only be registered with one File Sync Service at a time.

### Create and Configure a Container in Blob Storage

- Blobs are organized into containers within a storage account.
  - Every blob must belong to one container.
  - Individual containers can store an unlimited number of blobs.
- Containers can be created in the Azure portal or via CLIs/SDKs
  - Azure CLI: `az storage container create`
  - Azure PowerShell: `New-AzStorageContainer`
- Container configuration on creation:
  - Name: names must be unique within the storage account.
    - lowercase letters, numbers, and hyphens are the only permissable charcters. Names must begin with a letter or number and be 3-63 characters long.
  - Public Access Level: whether a container and its blobs can be accessed publicly. The default setting is private.
    - Private: all anonymous access is prohibited.
    - Blob: Allow anonymous public read access for the blobs only.
    - Container: Anonymous public read and list access granted on entire container.

### Configure Storage Tiers

- Four access tiers, each optimized to suit a particular pattern of data usage.
- **Hot Tier:** optimized for frequent reads and writes of objects.
  - Default access tier.
  - Lowest access cost, higher storage cost than Cool or Archive tiers.
- **Cool Tier:** optimized for storing large amounts of data that is infrequently accessed.
  - Intended to store data that will remain in the tier for at least 30 days.
  - Ideal for any data that is infrequently accessed but needs to be immediately available (short-term backup, disaster recovery).
  - Lower storage cost but higher access cost than Hot tier.
- **Archive Tier:** offline tier optimized for data that can handle several hours of retrieval latency.
  - Data must remain in the tier for at least 180 days; there is an early retrieval charge.
  - Ideal for secondary backups, original raw data, and compliance info.
  - Lowest storage cost, highest access cost of any tier.
- **Premium Blob Storage**: optimized for I/O intensive workloads requiring consistent low-latency storage.
  - Data is stored on SSDs.
  - Best for workloads performing many small transactions.
- Access tier can be changed at any time.

### Configure Snapshots and Soft Delete for Azure Files

- File share snapshots capture a point-in-time, read-only copy of the data stored in the share.
  - Snapshot capability is provided at the file share level (not granular).
  - Snapshots are incremental. Only the data changed since the last snapshot is captured. This minimizes the time it takes to create a snapshot and reduces storage costs.
  - To restore a share after accidental deletion or disaster, only the most recent snapshot needs to be retained.
  - Snapshots can be retrieved for individual files if needed.
  - To delete a share that has snapshots, the snapshots must be deleted first.

### Configure Blob Lifecycle Management

- Lifecycle management is configured as a rule-based policy for GPv2 and Blob Storage accounts.
- Policies transition data between tiers and sets expiration times for data:
  - Move to cooler tiers: Hot to Cool, Hot to Archive, Cool to Archive.
  - Delete blobs when they expire.
- Can configure rule based conditions to run daily at the storage account level, or apply them to specific containers or a subset of blobs.
- To create rules in the portal:
  - If-then block conditions are used to transition or expire data.
  - "Then" clause is executed when "If" clause evaluates to true.
  - If clause can be "last modified" or "created more than" `x` days ago.
  - Then clause options:
    - move to Cool storage
    - move to Archive storage
    - delete the blob

### Configure Blob Versioning
