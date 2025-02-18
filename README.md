# Microsoft Active Directory Security and Management Guide

This guide provides a comprehensive framework for installing, configuring, managing, and securing **Microsoft Active Directory (AD)**. Active Directory is the centralized repository for managing user accounts, computers, groups, and network resources. Emphasizing security best practices, automation using PowerShell, and effective management strategies, this guide is an invaluable resource for cybersecurity professionals.

---

## Table of Contents

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

## Overview

This guide details a comprehensive approach for the installation, configuration, management, and security of **Microsoft Active Directory (AD)**. The focus is on applying security best practices, automating administrative tasks using PowerShell, and ensuring efficient management of users, groups, and network resources.

**Key Features:**
- Installation and configuration of Active Directory.
- Best practices for user and group management.
- Implementation of security policies and auditing mechanisms.
- Automation of administrative tasks using PowerShell.
- Configuration and management of Group Policy Objects (GPOs).
- Centralized file management through home folder redirection.
- Security hardening techniques against common AD threats.

---

## Prerequisites

- A Windows Server machine (2019, 2022, or later).
- Administrator privileges on the server.
- Familiarity with PowerShell and Active Directory concepts.

---

## 1. Installation of Active Directory

### Installation Steps
1. Open **Server Manager** on your Windows Server.
2. Select **Add Roles and Features**.
3. Choose **Role-based or feature-based installation** and click **Next**.
4. Select your target server from the server pool and click **Next**.
5. From the **Server Roles** list, select **Active Directory Domain Services (AD DS)**.
6. Complete the wizard and then promote the server to a domain controller:
   - Click the notification flag in **Server Manager** and select **Promote this server to a domain controller**.
   - Follow the prompts to set up a new domain in a new forest.

### Verification
- Confirm a successful installation by accessing **Active Directory Users and Computers (ADUC)**.

---

## 2. Basic Active Directory Management

### User and Group Management

#### Creating Users
1. Open **Active Directory Users and Computers**.
2. Right-click the desired **Organizational Unit (OU)**.
3. Select **New > User** and follow the prompts to create the user account.

#### Creating Groups
1. Right-click the desired **OU**.
2. Select **New > Group**.
3. Choose a group type (Security or Distribution) and complete the required details.

### Group Policies Overview
1. Open **Group Policy Management** from the **Tools** menu in **Server Manager**.
2. Expand your domain tree under **Domains > [YourDomain].local**.
3. To create a new GPO:
   - Right-click the domain or OU and select **Create a GPO in this domain, and Link it here...**.
   - Name the GPO appropriately and click **OK**.

---

## 3. Security Hardening Active Directory

### Least Privilege Principle
- Enforce the least privilege principle by limiting administrative rights only to those who need them.
- Implement **Role-Based Access Control (RBAC)** to ensure users have access only to the resources necessary for their roles.

### Privileged Access Workstations (PAWs)
- Use **Privileged Access Workstations** exclusively for administrative tasks to reduce risk.
- Ensure PAWs are isolated from the rest of the network and run minimal services.

### Secure Password Policies
1. Enforce strong password policies:
   - Minimum password length: **12 characters**.
   - Password expiration: **90 days**.
   - Maintain at least **5 previous passwords**.
2. Configure these policies via **Group Policy Management**:
   - Navigate to **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy**.

### Multi-Factor Authentication (MFA)
- Implement MFA for administrative accounts and users accessing sensitive resources.
- Use available on-premises options to add an extra layer of security.

---

## 4. Active Directory Auditing and Monitoring

### Auditing Changes
1. Enable auditing through **Group Policy Management**:
   - Navigate to **Computer Configuration > Policies > Windows Settings > Security Settings > Advanced Audit Policy Configuration > Audit Policies > Directory Service Access**.
   - Enable **Audit Directory Service Changes** to monitor changes made to AD objects.

### SIEM Integration
- Integrate AD logs with a SIEM solution for real-time monitoring and alerting on suspicious activities.

### Backup and Recovery Plan
- Develop and document a comprehensive backup and recovery plan for Active Directory.
- Use tools such as Windows Server Backup or System State backups to protect AD data.

---

## 5. Automating Security Tasks with PowerShell

Automation helps reduce manual effort and human error.

### Automated User Auditing
Export AD user details (e.g., last logon dates) to a CSV file:

```powershell
Get-ADUser -Filter * -Properties LastLogonDate |
    Select-Object Name, LastLogonDate |
    Export-Csv -Path "C:\ADUserAudit.csv" -NoTypeInformation
```

### Automating Group Policy Changes
Import a baseline GPO configuration:

```powershell
Import-GPO -Path "C:\GPOs\AD_GPO_baseline.xml" -Domain "yourdomain.local"
```

### Password Policy Compliance Check
Check the current password policy:

```powershell
Get-ADDefaultDomainPasswordPolicy | Select-Object *
```

### Identifying Inactive Accounts
Find users who have been inactive for over 90 days:

```powershell
$InactiveDays = 90
$DateThreshold = (Get-Date).AddDays(-$InactiveDays)
Get-ADUser -Filter {LastLogonDate -lt $DateThreshold} |
    Select-Object Name, LastLogonDate |
    Export-Csv -Path "C:\InactiveUsers.csv" -NoTypeInformation
```

---

## 6. Mitigating Common Active Directory Security Threats

### Pass-the-Hash and Pass-the-Ticket Attacks
- Implement **Microsoft Local Administrator Password Solution (LAPS)** to ensure each device uses a unique, regularly rotated local admin password.
- Configure Kerberos to use a shorter ticket lifespan (e.g., 4 hours) to limit exposure.

### Securing Domain Controllers
- Disable unnecessary services on domain controllers.
- Limit network access to domain controllers.
- Apply security features such as Secure Boot, BitLocker, and Windows Defender Credential Guard.

---

## 7. Folder Redirection and Home Folders

### Creating Centralized Home Folders
1. Configure folder redirection via Group Policy:
   - Navigate to **User Configuration > Policies > Windows Settings > Folder Redirection**.
   - Right-click the **Documents** folder, select **Properties**, and set the root path to a centralized network share (e.g., `\\SGC-AD\home\%username%`).

---

## 8. Tools and Scripts

The repository includes several PowerShell scripts for automating routine AD tasks:
- **user_audit.ps1:** Audits user activity and logon details.
- **password_policy_audit.ps1:** Checks compliance with password policies.
- **account_expiry_check.ps1:** Identifies accounts nearing expiration.
- **inactive_users_check.ps1:** Detects inactive user accounts.
- **gpo_import.ps1:** Imports baseline GPO settings.
- **folder_redirection_setup.ps1:** Configures home folder redirection.

Each script is well-commented and includes a separate configuration file to simplify future updates.

---

## 9. Conclusion

This guide provides a complete resource for setting up, managing, and securing **Microsoft Active Directory**. It emphasizes best practices, automation, and security hardening techniques essential for maintaining a secure IT environment. This guide is designed to help system administrators and cybersecurity professionals streamline operations while reducing risk.

---

## 10. Resources

- [Best Practices for Securing Active Directory](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory)
- [Guide to Enterprise Password Management](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63-3.pdf)
- [Center for Internet Security](https://www.cisecurity.org/controls/)
