## Active Directory Hierarchy

This table shows the logical structure of a typical Active Directory environment, starting from the highest level (Forest) down to individual objects within Organizational Units (OUs).
## Hierarchy Comparison: On-Premises AD vs. Azure AD

This document explains the structural components of traditional On-Premises Active Directory (AD DS) and contrasts them with the flatter structure of the cloud-based Azure Active Directory (now Microsoft Entra ID).

### On-Premises Active Directory (AD DS) Hierarchy

Traditional AD uses a strict, nested hierarchy to organize and manage resources on an internal network.

* **1. Forest**
    * **Definition:** The highest-level container in Active Directory. It is the ultimate security boundary. All domains within a single forest share a common configuration and trust each other by default.
* **2. Tree**
    * **Definition:** A collection of one or more domains that share a contiguous DNS namespace (e.g., `mycompany.local` and its child `east.mycompany.local`). The first domain created in a forest becomes the root of the first tree.
* **3. Domain**
    * **Definition:** A core administrative boundary for managing users, computers, and groups that share a common security database and policies. Each domain has one or more Domain Controllers (DCs) to handle authentication.
* **4. Organizational Unit (OU)**
    * **Definition:** The smallest and most flexible container, used *within* a domain to organize objects like users, groups, and computers for easier management and delegation of permissions. Group Policies (GPOs) are typically linked to OUs.
* **5. Group Policy Objects (GPO)**
    * **Definition:** Group policy objects are rules that control access within a domain. These GPOs are set by the domain controller and can control access, behaviors and user profiles. GPOs can be set in the domain controller GUI under a tool called the Group Policy Management Console.

## Visual Hierarchy (On-Premises AD)
---
**Forest** (`mycompany.local`)
* **Domain (Parent):** `mycompany.local`
    * **Domain Controller:** DC01
    * **Organizational Unit (OU): Sales**
        * **User:** j.doe
        * **Group:** Sales Team
        * **Computer:** SALES-PC-01
        * **Linked GPO:** Sales Policies
    * **Organizational Unit (OU): IT**
        * **User:** admin.user
        * **Group:** IT Admins
        * **Computer:** IT-LAPTOP-05
        * **Linked GPO:** IT Restrictions
    * **Domain (Child):** `east.mycompany.local` (connected via a two-way trust)
        * **Domain Controller:** DC02
        * **Organizational Unit (OU): East Coast Sales**
            * **User:** s.smith
            * **Group:** East Sales Team

---

```mermaid
architecture
    group ad_forest(cloud)[AD Forest]
      group ad_tree(cloud)[Tree] in ad_forest
        group ou1(cloud)[Organizational Unit 1] in ad_tree
          service domainA(server)[Domain A] in ou1
        group ou2(cloud)[Organizational Unit 2] in ad_tree
          service domainB(server)[Domain B] in ou2
```
## Azure Active Directory comparison 
### Azure Active Directory / Microsoft Entra ID Hierarchy

Azure AD is a cloud-based identity service and does not use the same hierarchical structure. Its structure is fundamentally "flat."

* **1. Tenant**
    * **Definition:** A dedicated and trusted instance of Azure AD that an organization receives when it signs up for a Microsoft cloud service (like Microsoft 365 or Azure). The tenant holds all of the company's users, groups, and registered applications. It is the primary security and identity boundary in the cloud.
    * *Analogy: The entire company's secure, cloud-based account with Microsoft.*
* **2. Directory Objects (Flat Structure)**
    * **Definition:** Unlike On-Premises AD, objects in Azure AD are not nested within a complex hierarchy of OUs. They exist in a flat list directly within the tenant. While you can use "Administrative Units" for some delegation, it doesn't create the same nested structure as OUs.
    * **Objects Include:**
        * **Users:** Identities for people who need to access cloud resources.
        * **Groups:** Collections of users for assigning permissions to applications.
        * **Enterprise Applications:** Representations of SaaS apps (like Salesforce, Slack, etc.) that users can sign into their Azure AD credentials.
        * **App Registrations:** For applications you develop yourself that need to integrate with Azure AD.
        * **Devices:** Computers and mobile devices registered with the tenant.
