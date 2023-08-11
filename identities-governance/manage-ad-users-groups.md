# Manage Azure AD users and groups

## Modules

Below are links to the relevant modules for each listed learning objective. Modules that cover multiple objectives will be listed multiple times.

- Create users and groups
  - [Configure user and group accounts](https://learn.microsoft.com/en-us/training/modules/configure-user-group-accounts/)
- Manage user and group properties
  - [Configure user and group accounts](https://learn.microsoft.com/en-us/training/modules/configure-user-group-accounts/)
- Manage licenses in Azure AD
  - [Assign or remove licenses in the Azure portal](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/license-users-groups) (not part of the AZ-104 course material)
  - [What is group-based licensing in Azure Active Directory?](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/licensing-whatis-azure-portal) (not part of the AZ-104 course material)
- Manage external users
  - [Configure user and group accounts](https://learn.microsoft.com/en-us/training/modules/configure-user-group-accounts/)
- Configure self-service password reset (SSPR)
  - [Configure Azure Active Directory](https://learn.microsoft.com/en-us/training/modules/configure-azure-active-directory/)

## Relevant Exercises

- [Create Azure users and groups in Azure Active Directory](https://learn.microsoft.com/en-us/training/modules/create-users-and-groups-in-azure-active-directory/)
- [Allow users to reset their password with Azure Active Directory self-service password reset](https://learn.microsoft.com/en-us/training/modules/allow-users-reset-their-password/)

## Labs

- [Manage Azure Active Directory Identities](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_01-Manage_Azure_AD_Identities.html)

## Summary/Notes

### General

- **Azure Active Directory:** Microsoft's multi-tenanted cloud-based directory and identity management service.
  - Acts as SSO provider for many SaaS apps (ex. Salesforce, Workday, etc.)
  - Supports MFA, conditional access, RBAC
  - Can connect to Active Directory to extend on-prem instances to the cloud
- Concepts:
  - **Identity:** object that can be authenticated (ex. user, app, etc.)
  - **Account:** identity that has data associated with it; an account can't exist without an identity.
  - **Azure Active Directory Account:** an identity created through Azure Active Directory or Microsoft cloud service. The account is stored in Azure Active Directory and accessible to an organization's cloud service subscriptions.
  - **Azure Tenant/Directory:** a single dedicated and trusted instance of Azure AD, representing a single organization. It is possible to create multiple tenants/instances.
  - **Azure Subscription:** Used to pay for Azure cloud services; each subscription is joined to a single Azure AD tenant.
- Azure AD vs. Active Directory:
  - Azure Active Directory is most similar to **Active Directory Domain Services**
    - **Active Directory Domain Services:** a traditional deployment of Windows Server-based active directory. It is primarily a directory service, one component of many in Active Directory.
    - Azure AD is a full identity solution (incl. federation services and 3rd party services), not just a directory service.
    - Azure AD users and groups are created in a flat structure-- there are no organizational units or group policy objects
- Azure Active Directory Editions:
  - Free: included with Azure subscription. Has user and group management, on-premises directory sync capabilities, basic reports, and SSO for Azure, Microsoft 365, and SaaS apps.
  - Microsoft 365 Apps Edition: included with Microsoft 365. Has all features of free tier, as well as Identity and Access Management (IAM) for Microsoft 365 apps, branding, MFA, group access management, and self-serve password reset.
  - Premium P1 Edition: All the features of the Free Edition, and it lets hybrid users access on-prem and cloud resources, includes advanced administration, and Microsoft Identity Manager.
  - Premium P2 Edition: All the features of the P1 Edition, with Azure AD Identity Protection for risk-based conditional access, Privileged Identity Management for regulating admin access, and just-in-time access.

### Create Users and Groups

- Azure AD supports 3 kinds of user accounts that describe if the user was created on-premises or in the cloud, and whether the user is internal or external.
  - **Cloud Identity:** an identity defined only in Azure Active Directory; includes administrative accounts and users managed as part of an organization. When a cloud identity is removed from the primary directory, the account is deleted.
  - **Directory-Synchronized Identity:** an identity defined in an on-premises instance of Active Directory, synchronized to Azure Active Directory through Active Directory Connect.
  - **Guest User:** an identity defined outside of the Azure Active Directory tentant. Could be another cloud provider, an XBox LIVE account, or other.
- When choosing what account types to support in an Azure AD instance:
  - Consider where your users are defined
  - Consider how you want to support external users/contributors. Do you want to use the Guest User type?
  - It's common to support 2+ account types.
- User accounts can be created by an Azure AD Global admin or User admin.
- User accounts can be added through portal, Microsoft 365 Admin Centre, Microsoft Intune Admin Console, or the Azure CLI.
- All user accounts are granted a default set of permissions on creation. Each type has different level of access for scope of work.
  - Admins have (obviously) highest level of access, followed by regular Azure AD users, and finally guest accounts that have restricted access.
- Bulk user account creation:
  - Bulk create and delete operations use a CSV template for account data. Template can be downloaded from portal.
  - Can also download bulk lists.
- Two types of group accounts:
  - **Security Group:** a group account for managing member and computer access to shared resources.
  - **Microsoft 365 Group:** a group account for collaboration that gives members access to a shared mailbox, calendar, files, SharePoint site, etc.
  - Security groups must be created by an Azure AD Administrator.
  - Microsoft 365 Groups can be created by normal users as well as administrators.

### Manage User and Group Properties

- Managing user accounts:
  - Display name and account name are required to create a new user account; these are set by an admin and cannot be changed.
  - Profile data includes info like job title, company phone, etc.
    - Non-admin users can be allowed to control some of this information.
    - Global or User administrators can set presets for some profile data.
  - Account restore: included in your account managment plan. Deleted accounts can be restored for 30 days after they're deleted.
  - Can collect sign-in and audit log information for users.
- Managing group accounts:
  - Use Microsoft 365 groups to enable group access for guest users from outside your Azure AD org.
  - Three different ways to assign group membership:
    - Assigned: specific users are added to a group. Each user can have unique permissions.
    - Dynamic User: dynamic membership rules automatically add and remove group members.
    - Dynamic Device: for security groups only; dynamic membership rules automatically add and remove devices from security groups.
- Managing app & resource access with groups:
  - Azure AD can manage on-prem and cloud-based apps and resources using groups.
  - Access management is a combo of Azure AD roles (manage Azure AD resources like users, groups, billing, licensing) and Azure RBAC
- Administrative units:
  - Administrative units are used to restrict the administrative scope. (ex. in a university with many depts., administrative units can be created for each department).
  - Global admins and Privileged Role admins can manage administrative units.
  - Administrative units apply only to management permissions; interaction with other users/resources still possible through user permissions.

### Manage Licenses in Azure AD

- Many Azure AD services require users/groups to be licensed for access.
  - Licenses applied per tenant and don't transfer to other tenants.
- Usage location: not all services available in all areas; usage location must be specified for users.
  - When assigning licenses to a group or doing bulk updates, any user who doesn't have usage location specified will inherit the location of the Azure AD org.
- View and manage licenses in portal:
  - Need License Administrator account.
  - Azure portal > Azure AD > Licenses > All Products page shows total, assigned, available, and expiring soon numbers for all licensed products.
- Can assign licenses directly to a user, or to a group.
- Group licensing:
  - Licenses can be assigned to any **security** group (cloud-only, or synced from on-prem with Azure AD Connect).
  - When a license is assigned to a group, Azure AD ensures that each group member is assigned a license. New members added to the group are automatically assigned a license, and members who are removed automatically have their license revoked.
  - Eliminates need to automate license assignment via PowerShell.
  - Group licensing requires one of:
    - Azure AD Premium P1 or higher
    - Microsoft 365 Business Premium
    - Office 365: Enterprise E3, A3, GCC G3, E3 for GCCH, E3 for DOD, or higher
  - All Microsoft cloud services that require user-level licensing are supported.
  - If a user is a member of multiple groups with the same license assigned, the license will be consumed only once for that user (license collisions are automatically resolved by Azure AD).
  - If a license can't be assigned to a user (not enough licenses available, or conflicting license assignments), administrators can take corrective action.
- Manage user licenses using PowerShell with the Microsoft 365 PowerShell module (ex cmdlets: `Get-MsolAccountSku`, `New-MsolUserLicense`, `Remove-MsolUserLicense`).

### Manage External Users

- **Azure Active Directory Business-to-Business**: feature of Azure AD that allows external users controlled access to resources without taking on the responsibility of managing their identity.
  - Two main use cases:
    1. Inviting external collaborators
    2. Giving users access to other tenants in a multi-tenanted business.
- Guest users have restricted permissions; they are invited to the organization via email or direct link and sign in with their own work/school/personal account
- By default, Azure AD member users have permissions to invite guest users.

### Configure SSPR

- In Azure AD, any user can change their password if they are already signed in.
  - If the user is not signed in and they forgot their password (or it's expired), then the password needs to be reset.
  - Self-service password reset allows users to reset their password from the browser/Windows sign in screen-- no helpdesk ticket needed.
  - Azure Administrator accounts can always reset their own passwords.
  - Self-service password reset is available in Azure AD P1 and P2, Microsoft 365 Apps for Business, and Microsoft 365.
- 6 different ways to authenticate a password reset:
  1. Mobile app: install and register Microsoft Authenticator. Azure sends a notification to Authenticator when reset is initiated.
  2. Mobile app code: install and register Microsoft Authenticator. Enter code from app when resetting password.
  3. Email: Azure sends an authentication code to a provided email address.
  4. Mobile phone: Azure sends an authentication code via SMS or phone call to provided number.
  5. Office phone: Azure will send an automated call to a non-mobile phone. Authorize by pressing `#`.
  6. Security Questions: select questions and provide responses. Answer questions correctly to authorize a reset.
- Administrators can choose how many authentication methods to require.
  - If using security questions (not recommended), can specify the number of questions to be set up and the minimum number of correct answers required to authenticate.
  - Administrative accounts always require strong, two-method authentication, regardless of the settings configured for other users.
  - Security questions are not available for users with admin roles.
- Recommendations:
  - Enable at least two authentication methods.
  - Use mobile app notification or code as the primary authentication method.
  - Enable email or office phone as an alternative.
  - Mobile phone not recommended due to possibility for fraudulent messages.
  - Security questions are the least recommended authentication option.
- Administrators can choose how users are notified of password reset:
  - Notify users via primary and secondary email when password reset.
  - Notify all administrators when an admin account's password is reset.
- For hybrid on-prem/cloud Active directory, the password reset will have to be written back to the on-prem directory (can be deployed with Azure AD Connect or cloud sync).
- Self-service password reset has 3 state settings:
  1. Disabled: no one can use SSPR (default)
  2. Enabled: everyone in the organization can use SSPR
  3. Selected: only members of a specified security group can use SSPR (use when testing before rollout)
- Can enable SSPR in the portal:
  - Configure authentication methods, registration and confirmation frequency, notifications, customization.
  - Can provide custom help desk link.
