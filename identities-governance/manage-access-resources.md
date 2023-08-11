# Manage access to Azure resources

## Modules

Below are links to the relevant modules for each listed learning objective. Modules that cover multiple objectives will be listed multiple times.

- Manage built-in Azure roles
  - [Configure role-based access control](https://learn.microsoft.com/en-us/training/modules/configure-role-based-access-control/)
- Assign roles at different scopes
  - [Configure role-based access control](https://learn.microsoft.com/en-us/training/modules/configure-role-based-access-control/)
- Interpret access assignments
  - [Configure role-based access control](https://learn.microsoft.com/en-us/training/modules/configure-role-based-access-control/)

## Relevant Exercises

- [Secure your Azure resources with Azure role-based access control (Azure RBAC)](https://learn.microsoft.com/en-us/training/modules/secure-azure-resources-with-rbac/)

## Labs

- [Manage Subscriptions and RBAC](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_02a_Manage_Subscriptions_and_RBAC.html)

## Summary/Notes

### General

- **Azure Role-Based Access Control (RBAC):** fine-grained access management for resources in Azure, built on Azure Resource Manager.
- Core concepts:
  - **Security Principal:** an object representing something that requests access to resources; can be a user, group, service principal, or managed identity.
  - **Role Definition**: a set of permissions that lists allowed operations for any security principal to which it is assigned.
    - Azure RBAC includes built-in roles such as Reader, Contibutor, Owner, and User Access Administrator.
    - Custom roles can be created from scratch or by modifying existing roles.
  - **Scope:** The boundary for the requested level of access.
  - **Assignment:** the process of attaching a role definition to a security principal at a given scope.
- Roles are commonly built around requirements to fulfill job function
- Best practice: use principal of least access when considering both permissions and scope; assign roles that grant the minimum level of privilege at the smallest scope required to fulfill requirements.
- Role definitions:
  - Defined in JSON
  - Each set of permissions listed has a name (ex. "Actions", "NotActions") that describes the purpose of that set.
    - `Actions`: what actions are allowed
    - `NotActions`: what actions are disallowed
    - `DataActions`: how data can be changed/used
    - `AssignableScopes`: scopes where a role definition can be defined. Can be management groups, subscriptions, resource groups, or resources; assignable scopes are all given by IDs
  - Effective permissions are given by `Actions - NotActions`
- Azure RBAC vs. Azure AD roles:

  - Three role types to support access management in Azure:
    1. Classic subscription administrator roles
    2. Azure RBAC roles
    3. Azure AD admin roles
  - The classic roles (Account Administrator, Service Administrator, Co-Administrator) were used to control access when Azure was first released, they are not ARM roles (Azure RBAC roles are). They were assigned at subscription scope.

  |                   | Azure RBAC Roles                                                                              | Azure AD Admin Roles                                                                                             |
  | ----------------- | --------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
  | Access Management | Manages access to Azure resources                                                             | Manages access to Azure AD resources                                                                             |
  | Scope Assignment  | Scope can be specified at multiple levels from Management Group to resources                  | Scope is fixed at tenant level                                                                                   |
  | Role Definitions  | Can be defined in Azure portal, via Azure CLI or Azure PowerShell, ARM templates, or REST API | Roles are defined via the Azure admin portal, Microsoft 365 admin portal, or Microsoft Graph Azure AD PowerShell |

### Manage Built-in Azure Roles

- Over 100 built-in role definitions, defined for many categories like General, Networking, Storage, Database, etc.
- Examples:

  | Role        | Description                                                    | `Actions` Permissions | `NotActions` Permissions                                                                                                                              |
  | ----------- | -------------------------------------------------------------- | --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
  | Owner       | Allow all actions                                              | `*`                   | N/A                                                                                                                                                   |
  | Contributor | Allow all actions, except deleting or writing role assignments | `*`                   | <ul><li>`Microsoft.Authorization/*/Delete`</li><li>`Microsoft.Authorization/*/Write`</li><li>`Microsoft.Authorization/elevateAccess/Action`</li></ul> |
  | Reader      | Allow all read actions                                         | `*/Read`              | N/A                                                                                                                                                   |

- **Owner:** full access to all resources, and the right to delegate access to others. The former classic administrator roles are assigned this RBAC role at the subscription scope.
- **Contributor:** create and manage all types of Azure resources; can't grant access to others.
- **Reader:** view existing Azure resources.
- **User Access Administrator:** manage user access to resources.

### Assign Roles at Different Scopes

- Role assignments:
  - Control access to resources.
  - Revoke access by removing role assignment.
  - Resources inherit role assignments from parent resources.
- Effective permissions for a user are a combination of assigned roles and the scopes to which those roles are assigned.
  - ex. Consider a user assigned the Reader role at the subscription scope and the Contributor role at the resource group scope for a resource group called Networks.
    - The user can perform read actions on all resources in the subscription.
    - The user can only create/delete or perform other Contributor actions within the Networks resource group.

### Interperet Access Assignments

- Role assignments for a given scope (including inheritance) are listed in the "Access Control (IAM)" pane in the Azure Portal
  - Can use the IAM pane to view and assign access.
  - Use the IAM pane "View my access" button to view your own role assignments at a given scope.
  - Use the IAM pane "Check access" button to view the role assignments of another security prinicipal.
- Azure RBAC is an **allow model**: when a role is assigned it allows a user to perform certain actions (instead of denying them).
- Permissions are combined in an additive fashion: if one role allows read permissions and another allows write permissions, a user assigned both roles will have read and write permissions.
  - `NotActions` are subtracted from `Actions` to form effective permissions, so certain permissions can be denied.
