# **Microsoft Active Directory Security and Management Guide**

## **Table of Contents**

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Installation of Active Directory](#installation-of-active-directory)
   - [Installation Steps](#installation-steps)
   - [Verification](#verification)
4. [Basic Active Directory Management](#basic-active-directory-management)
   - [User and Group Management](#user-and-group-management)
     - [Creating Users](#creating-users)
     - [Creating Groups](#creating-groups)
   - [Group Policies Overview](#group-policies-overview)
5. [Security Hardening Active Directory](#security-hardening-active-directory)
   - [Least Privilege Principle](#least-privilege-principle)
   - [Privileged Access Workstations (PAWs)](#privileged-access-workstations-paws)
   - [Secure Password Policies](#secure-password-policies)
   - [Multi-Factor Authentication (MFA)](#multi-factor-authentication-mfa)
6. [Active Directory Auditing and Monitoring](#active-directory-auditing-and-monitoring)
   - [Auditing Changes](#auditing-changes)
   - [SIEM Integration](#siem-integration)
   - [Backup and Recovery Plan](#backup-and-recovery-plan)
7. [Automating Security Tasks with PowerShell](#automating-security-tasks-with-powershell)
   - [Automated User Auditing](#automated-user-auditing)
   - [Automating Group Policy Changes](#automating-group-policy-changes)
   - [Password Policy Compliance Check](#password-policy-compliance-check)
   - [Identifying Inactive Accounts](#identifying-inactive-accounts)
8. [Mitigating Common Active Directory Security Threats](#mitigating-common-active-directory-security-threats)
   - [Pass-the-Hash and Pass-the-Ticket Attacks](#pass-the-hash-and-pass-the-ticket-attacks)
   - [Securing Domain Controllers](#securing-domain-controllers)
9. [Folder Redirection and Home Folders](#folder-redirection-and-home-folders)
   - [Creating Centralized Home Folders](#creating-centralized-home-folders)
10. [Tools and Scripts](#tools-and-scripts)
11. [Conclusion](#conclusion)
12. [Resources](#resources)

---

## **Overview**
This guide provides a comprehensive framework for the installation, configuration, management, and security of **Microsoft Active Directory (AD)**. Active Directory serves as a centralized repository for managing user accounts, computers, groups, and network resources, enabling efficient user authentication and authorization. This guide emphasizes **security best practices**, automation, and effective management strategies, making it invaluable for IT professionals and cybersecurity practitioners.

### **Key Features:**
- Installation and configuration of Active Directory.
- Best practices for user and group management.
- Implementation of security policies and auditing mechanisms.
- Automation of administrative tasks using PowerShell scripts.
- Configuration and management of Group Policy Objects (GPOs).
- Centralized file management through home folder redirection.
- Security hardening techniques against prevalent AD threats.

---

## **Prerequisites**
- A Windows Server machine (2019, 2022, or later).
- Administrator privileges on the server.
- Familiarity with PowerShell and Active Directory concepts.

---

## **1. Installation of Active Directory**

### **Installation Steps**
1. Open **Server Manager** on your Windows Server.
2. Select **Add Roles and Features**.
3. Choose **Role-based or feature-based installation** and click **Next**.
4. Select the server from the server pool and click **Next**.
5. From the **Server Roles** list, select **Active Directory Domain Services (AD DS)**.
6. Complete the wizard and promote the server to a domain controller:
   - Click on the notification flag in **Server Manager** and select **Promote this server to a domain controller**.
   - Follow the prompts to set up a new domain in a new forest.

### **Verification**
- Confirm successful installation by accessing **Active Directory Users and Computers (ADUC)**.

---

## **2. Basic Active Directory Management**

### **2.1 User and Group Management**

#### **Creating Users**
1. Open **Active Directory Users and Computers**.
2. Right-click the desired **Organizational Unit (OU)**.
3. Select **New > User**, and follow the prompts to create the user account.

#### **Creating Groups**
1. Right-click the desired **OU**.
2. Select **New > Group**.
3. Choose a group type (Security or Distribution) and fill in the required details.

### **2.2 Group Policies Overview**
1. Open **Group Policy Management** from the **Tools** menu in **Server Manager**.
2. Expand your domain tree under **Domains > [YourDomain].local**.
3. To create a new GPO:
   - Right-click the domain or OU and select **Create a GPO in this domain, and Link it here...**.
   - Name the GPO appropriately and click **OK**.

---

## **3. Security Hardening Active Directory**

### **3.1 Least Privilege Principle**
- **Enforce the Least Privilege Principle**:
  - Limit administrative rights to only those who need them.
  - Implement **Role-Based Access Control (RBAC)** for more granular permissions, ensuring that users only have access to resources necessary for their job functions.

### **3.2 Privileged Access Workstations (PAWs)**
- Utilize **Privileged Access Workstations** for administrative tasks to mitigate risks associated with malware and unauthorized access. PAWs should be isolated from the rest of the network and run minimal services.

### **3.3 Secure Password Policies**
1. Enforce strong password policies:
   - Minimum password length: **12 characters**.
   - Password expiration: **90 days**.
   - Password history: Maintain at least **5 previous passwords**.

2. To set these policies:
   - Open **Group Policy Management**.
   - Navigate to **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy**.

### **3.4 Multi-Factor Authentication (MFA)**
- Implement MFA for administrative accounts and users accessing sensitive resources. Use Azure AD MFA or Windows Hello for Business as secure options.

---

## **4. Active Directory Auditing and Monitoring**

### **4.1 Auditing Changes**
1. Enable auditing:
   - Open **Group Policy Management**.
   - Navigate to **Computer Configuration > Policies > Windows Settings > Security Settings > Advanced Audit Policy Configuration > Audit Policies > Directory Service Access**.
   - Enable **Audit Directory Service Changes** to monitor changes made to AD objects.

### **4.2 SIEM Integration**
- Integrate Active Directory logs with a **Security Information and Event Management (SIEM)** solution (e.g., Microsoft Sentinel, Splunk) for real-time monitoring and alerting on suspicious activities.

### **4.3 Backup and Recovery Plan**
- Create and document a comprehensive backup and recovery plan for Active Directory to ensure business continuity. Utilize tools like Windows Server Backup or System State backup to protect AD data.

---

## **5. Automating Security Tasks with PowerShell**

### **5.1 Automated User Auditing**
Use scripts for regular audits of user accounts and group memberships. Example PowerShell script for auditing user logins:

```powershell
# Example PowerShell script for user auditing
Get-ADUser -Filter * | Select-Object Name, LastLogonDate | Export-Csv -Path "C:\ADUserAudit.csv" -NoTypeInformation
```
This retrieves all AD user accounts with their last login dates and exports the information to a CSV file.

### **5.2 Automating Group Policy Changes**
Use PowerShell scripts to apply and audit GPOs. Example:

```powershell
# Example to import a GPO
Import-GPO -Path "C:\GPOs\AD_GPO_baseline.xml" -Domain "yourdomain.local"
```

### **5.3 Password Policy Compliance Check**
To check compliance with your organization’s password policy:

```powershell
# Check current password policy
Get-ADDefaultDomainPasswordPolicy | Select-Object *
```

### **5.4 Identifying Inactive Accounts**
Identify inactive user accounts:

```powershell
# Find users inactive for over 90 days
$InactiveDays = 90
$DateThreshold = (Get-Date).AddDays(-$InactiveDays)
Get-ADUser -Filter {LastLogonDate -lt $DateThreshold} | Select-Object Name, LastLogonDate | Export-Csv -Path "C:\InactiveUsers.csv" -NoTypeInformation
```

---

## **6. Mitigating Common Active Directory Security Threats**

### **6.1 Pass-the-Hash and Pass-the-Ticket Attacks**
- Implement **Microsoft Local Administrator Password Solution (LAPS)** for secure local admin password management. This helps ensure unique local admin passwords across devices.
- Configure Kerberos with a **short ticket lifespan** (e.g., 4 hours) to reduce exposure to ticket-based attacks.

### **6.2 Securing Domain Controllers**
- Disable unnecessary services and limit network access to **Domain Controllers**. Employ **Secure Boot**, **BitLocker**, and **Windows Defender Credential Guard** for enhanced security.

---

## **7. Folder Redirection and Home Folders**

### **7.1 Creating Central

ized Home Folders**
1. Configure folder redirection:
   - Open **Group Policy Management**.
   - Navigate to **User Configuration > Policies > Windows Settings > Folder Redirection**.
   - Right-click on the **Documents** folder, select **Properties**, and set the root path to a centralized network share: `\\SGC-AD\home\%username%`.

---

## **9. Tools and Scripts**
The repository contains useful **PowerShell scripts** for automating administrative tasks:
- **user_audit.ps1:** Audits user activity and logins.
- **password_policy_audit.ps1:** Checks compliance with password policies.
- **account_expiry_check.ps1:** Identifies accounts nearing expiration.
- **inactive_users_check.ps1:** Identifies user accounts that have been inactive for a specified period.

---

## **10. Conclusion**
This guide serves as a comprehensive resource for setting up, managing, and securing **Microsoft Active Directory**. It emphasizes security hardening, automation, and best practices that are essential for maintaining a secure IT environment. This guide is suitable for both **system administrators** and professionals focused on **cybersecurity**.

---

## **8. Resources**
- **Microsoft Documentation on Active Directory Security**: [Best Practices for Securing Active Directory](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).
- **National Institute of Standards and Technology (NIST)**: [Guide to Enterprise Password Management](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63-3.pdf).
- **CIS Controls**: [Center for Internet Security](https://www.cisecurity.org/controls/).
