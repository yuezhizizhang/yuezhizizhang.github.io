---
layout: post
title:  "Try with Azure AD"
categories: "aad intune"
---

I've been working on Microsoft Azure AD, Intune and MDM for Q4 of 2020. 2 days ago, a colleague asked me a simple question about Azure AD. It prompts me with this post, to demonstrate how to try Microsoft Azure AD and Intune for free, and how to sync your On-Premise AD to Azure AD.

### Try Azure AD and Intune for free

To apply for a free Microsoft Intune account, please follow the steps from [Quickstart - Try Microsoft Intune for Free](https://docs.microsoft.com/en-us/mem/intune/fundamentals/free-trial-sign-up){:target="_blank"}. You are able to create a subdomain of **onmicrosoft.com** domain.

![Cascading Selector](/assets/2021-01-09-intune-trial-account.png "intune trial account")

After the account is created, login [Azure Portal](https://portal.azure.com/){:target="_blank"} with the new user account. View **Manage Azure Active Directory**. Navigate to [Azure AD Licenses](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Licenses){:target="_blank"}. Click "Get a free trial". Activate "AZURE AD PREMIUM P2" license to have a full-fledged experience of Azure AD.

![Cascading Selector](/assets/2021-01-09-azure-ad-premium-p2.png "AZURE AD PREMIUM P2")

### Sync Azure AD with On-Premise AD

Go to your On-Premise Active Directory server, download and install [Microsoft Azure Active Directory Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594){:target="_blank"}. Choose express installation, enter your Azure AD account and AD forest account. Click Sync and AAD Connect will sync your On-Premise AD to Azure AD. 

If you don't have an On-Premise AD server, you can run the following PowerShell commands on an VM to setup an AD server.

```powershell
# Install Feature Active Directory Domain Services
Install-WindowsFeature AD-Domain-Services
Import-Module ADDSDeployment
 
# Install Feature Remote Server Administration Tools
Install-WindowsFeature RSAT-ADDS-Tools
 
# Installs a new forest named odjtest.local
$SecurePassword = ConvertTo-SecureString "password" -AsPlainText -Force
$DomainName = "contoso.com"
Install-ADDSForest -DomainName $DomainName -SafeModeAdministratorPassword $SecurePassword -Force
```

I strongly recommend you read Microsoft tutorial [Integrate a single AD forest using password hash sync (PHS)](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/tutorial-password-hash-sync){:target="_blank"}. It tells you every step from setting up an AD forest on an VM to sync with Azure AD via AAD Connect. 

Azure AD Connect sync occurs at a scheduled interval. By default, it runs every 30 minutes. Please read [Azure AD Connect sync: Scheduler](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler){:target="_blank"} to learn more. To force an immediate sync, run PowerShell commands below on the AD server:

```powershell
Import-Module ADSync

# Get current sync settings
Get-ADSyncScheduler

# Start a delta sync
Start-ADSyncSyncCycle -PolicyType Delta

# Start a full sync
Start-ADSyncSyncCycle -PolicyType Initial
```

### Try with Microsoft Graph API

Now, you can sign in to [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer){:target="_blank"} with your new Azure AD account. Since you are the administrator of your Azure AD, you can grant yourself whatever permission you need to try every Graph API.