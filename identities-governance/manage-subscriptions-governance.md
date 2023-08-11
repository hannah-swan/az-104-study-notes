# Manage Azure subscriptions and governance

## Modules

Below are links to the relevant modules for each listed learning objective. Modules that cover multiple objectives will be listed multiple times.

- Implement and manage Azure Policy
  - [Configure Azure Policy](https://learn.microsoft.com/en-us/training/modules/configure-azure-policy/)
- Configure resource locks
  - [Lock your resources to protect your infrastructure](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/lock-resources?tabs=json#considerations-before-applying-your-locks) (not part of AZ-104 course material)
- Apply and manage tags on resources
  - [Configure Subscriptions](https://learn.microsoft.com/en-us/training/modules/configure-subscriptions/)
- Manage resource groups
  - [Manage Azure resource groups by using the Azure portal](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal) (not part of AZ-104 course material)
  - [Manage Azure resource groups by using the Azure CLI](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-cli) (not part of AZ-104 course material)
  - [Manage Azure resource groups by using Azure PowerShell](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-powershell) (not part of AZ-104 course material)
- Manage subscriptions
  - [Configure Subscriptions](https://learn.microsoft.com/en-us/training/modules/configure-subscriptions/)
- Manage costs by using alerts, budgets, and Azure Advisor recommendations
  - [Configure Subscriptions](https://learn.microsoft.com/en-us/training/modules/configure-subscriptions/)
- Configure management groups
  - [Configure Azure Policy](https://learn.microsoft.com/en-us/training/modules/configure-azure-policy/)

## Relevant Exercises

## Labs

- [Manage Governance via Azure Policy](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_02b-Manage_Governance_via_Azure_Policy.html)

## Summary/Notes

### General

- **Azure Region:** geographical area containing at least one, but possibly multiple, Azure datacenters.
  - There are \>60 Azure Regions in \>140 countries in which Azure is generally available.
  - Regions preserve data residency and offer compliance and resilience options.
- Region pairs: Most Azure regions are paired with another region in the same geography to support high-availability of Azure services.
  - Datacenters are physically separated (typically by at least 300 miles) to reduce the likelihood of a natural disaster affecting both datacenters simultaneously.
  - Some services will automatically replicate to both datacenters in a pair (for example, Geo-redundant storage).
  - In the event of a widespread Azure outage, one region out of each affected pair is prioritized to ensure service for the pair is resumed more quickly.
  - Updates are rolled out to paired regions in sequence to limit downtime and minimize risk.
  - With the exception of Brazil South and Singapore, paired regions reside within the same geography to meet data residency and legal jurisdiction requirements.
- Not all Azure services are supported in all Azure regions (ex. VM SKUs, storage types).
- Some services are "global" and don't require an Azure Region (ex. Azure AD, Traffic Manager, DNS).
- [Azure Global Infrastructure website](https://azure.microsoft.com/en-ca/explore/global-infrastructure/) lists supported regions, services, and region pairs.

### Implement and Manage Azure Policy

- **Azure Policy:** service in Azure that enables the creation, assignment, and management of policies to control and audit resources.
  - Offers built-in policies and the option to create your own custom policies.
  - Policies can be created for all resource types.
  - Real-time evaluation and enforcement, or periodic or on-demand evaluation.
- General features & uses of Azure Policy:
  - Apply policies at scale using management groups-- control compliance accross the organization. Can also define an exclusion scope for areas that shouldn't be bound by policies.
  - Can apply multiple policies or aggregate policy states using policy initiatives.
  - Remediation: use a managed identity to perform remediation of non-compliant resources on their creation, and remediate existing non-compliant resources.
  - Use Azure Policy to exercise governance:
    - Manage multiple subscriptions;
    - Standardize and enforce how cloud resources are configured; use policies to audit non-compliance;
    - Manage regulatory compliance, cost control, design consistency.
- **Policy Definition:** describes compliance conditions for a resource and actions to complete if a given resource meets the conditions. Essentially an if-then statement.
- **Initiative Definition:** a grouping of policy definitions. Used to control the scope at which policies are applied and to evaluate compliance of resources.
- Using policies and initiatives is a 4-step process:
  1. Create policy definitions: use built-in definitions or custom (ex. prevent VMs from being created if they're associated with a public IP address).
     - Accessing built-in policy definitions: can sort list by category. Examples include Allowed VM Size SKUs, Allowed Locations, etc.
     - Can import custom definitions from the GitHub samples repo, or edit built-ins to better suit your needs.
     - Can create net-new policies; all new policies must follow the JSON structure for policy definitions (see [here](https://learn.microsoft.com/en-us/azure/governance/policy/concepts/definition-structure)).
  2. Create an initiative definition: can create custom initiatives, or choose from pre-existing, built-in initiatives (ex. group policies that align with SOC2 controls into a "SOC2" initiative).
     - Accessing built-in initiatives: like policy definitions, built-in initiatives can are grouped by category. Example initiatives include Audit Machines with Insecure Password Security Settings, and ISO 27001:2013
     - Custom initiative definitions must be written in the prescribed JSON structure (see [here](https://learn.microsoft.com/en-us/azure/governance/policy/concepts/initiative-definition-structure)).
  3. Scope the initiative definition: Azure Policy allows for control over how initiative definitions are applied. Can be applied at the management group, subscription, or resource group scope.
     - Determine scope by assigning the initiative. Can choose affected subscriptions, resource groups, etc.
  4. Determine Compliance: Evaluate the state of compliance for all resources within the scope. Can choose to exempt resources/resource groups/subscriptions as-needed.
     - Use the Compliance feature in the Azure Portal to evaluate the compliance state of all your resources.

### Configure Resource Locks

- Two flavours of resource lock:
  - **CanNotDelete:** authorized users can read and modify a resource, but no one can delete it.
  - **ReadOnly:** authorized users can read a resource, but no one can modify, update, or delete it. Similar to forcing the "Reader" role on all authorized users.
- Locks are inherited by child resources.
  - For example, a "CanNotDelete" lock placed on a resource group will prevent all resources in the resource group from being deleted.
  - An attempt to delete a resource group will be blocked if it contains a resource that has a delete lock on it.
- Locks apply only to control plane operations! Data plane operations are unchanged by the presence of a lock.
  - For example, a read-only lock on a storage account prevents the settings of the storage account from being modified, but data can still be added to and removed from the storage account.
- Resource locks have some quirks. See [here](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/lock-resources?tabs=json#considerations-before-applying-your-locks) for a list.
- Resource locks can be configured in the Azure portal, via the Azure CLI/Azure PowerShell, or in ARM templates.
- Azure services which use managed applications will create a locked resource group containing infrastructure. The lock cannot be removed (owned by system application), and the resource group cannot be deleted without removing the lock.
  - To delete these resources, delete the service itself. The related, locked resources will be removed as part of the process.

### Apply and Manage Tags on Resources

- **Resource Tag:** a name-value pair that can be added to resources to group them for billing or organizational purposes.
  - Tags can be chosen from a predefined set, or unique to a resource.
  - Each resource or resource group can have up to 50 tags.
- Resource tags are _not_ inherited by child resources.
- Tags are searchable: can query tag name and value.
- Tags are exported to CSV along with resource usage.
- Can apply tags in the portal, via Azure PowerShell, or with the Azure CLI.

### Manage Resource Groups

- **Resource group:** a container holding related resources in Azure; intended to hold all resources for a solution that should be managed as a group. Typically all resources in a resource group will have the same lifecyclye.
  - Resource groups store metadata about the resources which they contain; if data residency is a concern, the location for a resource group should be chosen to meet compliance requirements.
- All resources must belong to a resource group, and resources can only belong to a single resource group.
- Resource groups can be created and managed via Azure portal, Azure CLI, or Azure PowerShell
  - Azure CLI commands: `az group create`, `az group list`, `az group show`, `az group delete`
  - Azure PowerShell commands: `New-AzResourceGroup`, `Get-AzResourceGroup`, `Remove-AzResourceGroup`

### Manage Subscriptions

- **Azure Subscription:** a logical unit of Azure services linked to an Azure account, which helps to organize access to Azure services and control how resource usage is reported, billed, and paid.
- Azure is billed per-subscription. An organization can have many subscriptions, each with different billing information.
- Relationship between accounts and subscriptions is not 1-1; can have many subscriptions linked to one account and many accounts can be linked to one subscription.
  - Accounts can be Azure AD accounts or a directory that's trusted by Azure AD (ex. a personal Microsoft account, or Microsoft work/school account is trusted by Azure AD).
- Every subscription can be associated to an Azure AD tenant, which allows users and services to authenticate with Azure AD.
- Obtaining a subscription:
  - Enterprise Agreement (EA): Azure can be added to an existing EA; requires upfront monetary commitment for consumption of Azure resources.
  - Microsoft reseller: purchase through Open Licensing program from Microsoft reseller.
  - Microsoft partner: purchase services through a Microsoft partner who can design and implement Azure cloud solution for you.
  - Personal free account: start a free trial with your personal account.
- Common subscription types:
  - Free: includes a monetary credit to spend in first 30 days of use, and free access to many popular services for 12 months
  - Pay-as-you-go: charges for Azure services are paid monthly, you pay only for what you use.
  - Enterprise Agreement: buy cloud services and software licenses under one agreement, making an upfront commitment for usage.
  - Students: monetary credit for services to be used within the first 12 months of activation; can select free services without providing payment information. Requires verification of student status.

### Manage Costs by Using Alerts, Budgets, and Azure Advisor Recommendations

- **Microsoft Cost Management:** features providing support for administrative billing tasks; can be used to monitor and control Azure spending and optimize resource usage.
  - Shows organizational cost and usage patterns, advanced analytics, and cost predictions.
  - Factors in negotiated prices, reservations, and Hybrid Benefit discounts.
  - Uses management groups, budgets, recommendations to show expenses and options for cost reduction.
- Cost analysis feature: view aggregated costs, spending trends. Make spending estimates using historical data.
  - Show usage-based costs for Azure and 3rd party Marketplace services.
  - Limitation: changes such as reservation, support purchases, and taxes are not always visible in the cost displays.
- Budget feature: prevent spending limits from being surpassed. Get notified when cost approaches configured thresholds.
- Recommendation feature: optimize spending and improve efficiency of resource usage.
- Export feature: easily export data; can be integrated with 3rd party tools using the Azure portal or APIs.
- Cost savings:
  - Reservations: pay ahead for 1 or 3 years of VM, SQL Database compute capacity, Cosmos DB throughput, etc. Up to 72% savings over pay-as-you-go.
  - Hybrid Benefit: pricing benefits for license holders with Software Assurance. Use existing on-prem Windows Server or SQL Server licenses in the cloud.
  - Azure Credits: Visual Studio subscribers get a monthly Azure credit.
  - Regions: Some regions are less expensive than others. Plan accordingly.
  - Budgets: Prevent over-spending and organize usage.
  - Pricing Calculator: use to estimate cost of Azure services.

### Configure Management Groups

- **Management Group:** a level of scope above subscriptions; managment groups act as containers to manage access, policy, and compliance across subscriptions.
- By default, all new subscriptions are placed under a top-level management group (the root group)
- Management group trees can have up to six levels of depth.
  - Use management group trees to create custom hierarchies and groupings that can target policies and spend budgets across subscriptions.
- RBAC not enabled by default for management groups.
- Uses for management groups:
  - Use policy inheritance with management groups to control resources from the initial group into each subscription and resource group under it.
    - For example, restrict the regions in which VMs can be created by creating and assigning a policy at the management group level.
  - Use managment groups to meet compliance rules on a per-department basis.
  - Refine cost reporting and management.
- Create management groups in Azure portal, or with Azure CLI/Azure PowerShell
