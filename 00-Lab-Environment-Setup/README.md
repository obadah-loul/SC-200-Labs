# 00 - Lab Environment Setup

## Objective

The objective of this lab is to create a Microsoft SC-200 lab environment from scratch using Microsoft Sentinel, Log Analytics Workspace, Windows Security Events, Azure Arc, and KQL validation.

This lab documents the full setup process and includes screenshot evidence for each major step.

## Full Lab Document

The Word version of this lab is available here:

[Download the SC-200 Sentinel Lab Environment Setup document](SC-200-Sentinel-Lab-Environment-Setup.docx)

---

## Lab Summary

In this lab, I completed the following:

- Created an Azure Resource Group
- Created a Log Analytics Workspace
- Enabled Microsoft Sentinel
- Opened Sentinel Content Hub
- Installed Windows Security Events content
- Opened the Windows Security Events via AMA connector
- Onboarded a Windows machine using Azure Arc
- Created a Data Collection Rule
- Connected the machine to Sentinel
- Validated Windows Security Event logs using KQL

---

# 1. Create a Resource Group

## Purpose

The resource group is used to organize all Azure resources for this SC-200 lab.

## Portal Location

Azure Portal → Resource groups → Create

## Configuration Used

| Setting | Value |
|---|---|
| Resource group name | `rg-sc200-lab-sea-2` |
| Region | `Southeast Asia` |

## Evidence

![Create Resource Group](screenshots/01-create-resource-group.png)

## Result

The resource group was created successfully.

---

# 2. Create Log Analytics Workspace

## Purpose

The Log Analytics Workspace stores logs that Microsoft Sentinel will use for detection, investigation, and hunting.

## Portal Location

Azure Portal → Log Analytics workspaces → Create

## Configuration Used

| Setting | Value |
|---|---|
| Resource group | `rg-sc200-lab-sea-2` |
| Workspace name | `law-sc200-lab-sea-2` |
| Region | `Southeast Asia` |

## Evidence

![Create Log Analytics Workspace](screenshots/02-create-log-analytics-workspace.png)

## Result

The Log Analytics Workspace was created successfully.

---

# 3. Enable Microsoft Sentinel

## Purpose

Microsoft Sentinel is enabled on top of the Log Analytics Workspace so that the workspace can be used for SIEM features.

## Portal Location

Azure Portal → Microsoft Sentinel

## Steps Completed

1. Opened Microsoft Sentinel.
2. Selected the Log Analytics Workspace.
3. Added Sentinel to the workspace.

## Evidence

![Enable Microsoft Sentinel](screenshots/03-enable-microsoft-sentinel-on-workspace.png)

## Result

Microsoft Sentinel was enabled on the workspace.

---

# 4. Open Sentinel Content Hub

## Purpose

Content Hub is used to install Microsoft Sentinel solutions, connectors, analytics templates, hunting queries, and workbooks.

## Portal Location

Microsoft Sentinel → Content Hub

## Evidence

![Azure Portal Content Hub Moved to Defender](screenshots/04-azure-portal-content-hub-moved-to-defender.png)

![Defender Sentinel SIEM Workspaces Connected](screenshots/05-defender-sentinel-siem-workspaces-connected.png)

![Open Sentinel Content Hub](screenshots/06-open-sentinel-content-hub.png)

## Result

The Sentinel Content Hub was opened successfully.

---

# 5. Install Windows Security Events Content

## Purpose

The Windows Security Events solution provides the connector and supporting content required to collect Windows Security logs.

## Steps Completed

1. Opened Content Hub.
2. Searched for `Windows Security Events`.
3. Selected the Windows Security Events content.
4. Installed the content.

## Evidence

![Search Windows Security Events Content](screenshots/07-search-windows-security-events-content.png)

![Windows Security Events Content Installed](screenshots/08-windows-security-events-content-installed.png)

## Result

Windows Security Events content was installed successfully.

This added:

- Windows Security Events connector
- Analytics rule templates
- Hunting queries
- Workbooks

---

# 6. Open Windows Security Events via AMA Connector

## Purpose

The Windows Security Events via AMA connector is used to collect Windows Security logs through Azure Monitor Agent.

## Portal Location

Microsoft Sentinel → Data connectors

## Steps Completed

1. Opened Data connectors.
2. Searched for Windows Security Events.
3. Opened Windows Security Events via AMA.
4. Reviewed the connector page.

## Evidence

![Windows Security Events via AMA Connector](screenshots/09-windows-security-events-via-ama-connector.png)

![Windows Security Events Connector Details](screenshots/10-windows-security-events-connector-details.png)

![Data Connectors List Windows Security Events](screenshots/11-data-connectors-list-windows-security-events.png)

## Result

The Windows Security Events via AMA connector page was opened successfully.

---

# 7. Onboard Windows Machine Using Azure Arc

## Purpose

Because this lab used a personal Windows machine instead of an Azure VM, Azure Arc was used to connect the machine to Azure.

## Portal Location

Azure Portal → Azure Arc → Machines

## Configuration Used

| Setting | Value |
|---|---|
| Resource group | `rg-sc200-lab-sea-2` |
| Region | `Southeast Asia` |
| Operating system | `Windows` |
| Connectivity method | `Public endpoint` |

## Evidence

![Azure Arc Machines Empty Before Onboarding](screenshots/12-azure-arc-machines-empty-before-onboarding.png)

![Azure Arc Onboard Existing Machines](screenshots/13-azure-arc-onboard-existing-machines.png)

![Azure Arc Onboarding Settings](screenshots/14-azure-arc-onboarding-settings.png)

## PowerShell Commands Used

```powershell
cd $env:USERPROFILE\Downloads
```

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

```powershell
.\OnboardingScript.ps1
```

## Evidence

![Run Azure Arc Onboarding Script](screenshots/15-run-azure-arc-onboarding-script-redacted.png)

![Azure Arc Machine Connected](screenshots/16-azure-arc-machine-connected.png)

## Result

The Windows machine was connected to Azure Arc successfully.

---

# 8. Create Data Collection Rule

## Purpose

The Data Collection Rule controls which logs are collected from the Windows machine and sent to Microsoft Sentinel.

## Portal Location

Microsoft Sentinel → Data connectors → Windows Security Events via AMA

## Rule Configuration

| Setting | Value |
|---|---|
| Rule name | `dcr-sc200-windows-security-events` |
| Resource group | `rg-sc200-lab-sea-2` |
| Event collection level | `Common` |

## Evidence

![Create Data Collection Rule Basic](screenshots/17-create-data-collection-rule-basic.png)

![Data Collection Rule Basic Configured](screenshots/18-data-collection-rule-basic-configured.png)

![Select Arc Machine for Data Collection](screenshots/19-select-arc-machine-for-data-collection-rule.png)

![Select Common Security Events](screenshots/20-select-common-security-events.png)

![Review Create Data Collection Rule](screenshots/21-review-create-data-collection-rule.png)

## Important Note

I selected **Common** instead of **All Security Events** to avoid collecting too many logs and wasting Azure credits.

## Result

The Data Collection Rule was created successfully.

---

# 9. Confirm Data Collection Rule Connection

## Purpose

After creating the Data Collection Rule, I verified that the rule was connected to the Windows Security Events via AMA connector.

## Evidence

![Data Collection Rule Connected](screenshots/22-data-collection-rule-connected.png)

![Confirm Data Collection Rule Common Events](screenshots/23-confirm-data-collection-rule-common-filter.png)

## Result

The Data Collection Rule appeared in the connector page and confirmed that the machine was connected for Windows Security Event collection.

---

# 10. Validate Logs with KQL

## Purpose

KQL was used to confirm that Windows Security Event logs were arriving in Microsoft Sentinel.

## Portal Location

Microsoft Sentinel → Logs

## Query Used

```kql
SecurityEvent
| take 10
```

## Backup Query

```kql
SecurityEvent
| count
```

## Evidence

![SecurityEvent Logs Confirmed in Sentinel](screenshots/24-securityevent-logs-confirmed-in-sentinel.png)

## Result

Microsoft Sentinel successfully received Windows Security Event logs.

Confirmed details:

- `SecurityEvent` table contained logs
- Source: `Microsoft-Windows-Security-Auditing`
- Channel: `Security`
- Machine connected through Azure Arc

---

# What I Learned

In this lab, I learned how to:

- Create an Azure Resource Group
- Create a Log Analytics Workspace
- Enable Microsoft Sentinel
- Install Sentinel content from Content Hub
- Configure Windows Security Events via AMA
- Onboard a Windows machine using Azure Arc
- Create a Data Collection Rule
- Validate log ingestion using KQL
- Document lab evidence in GitHub using Markdown and screenshots

---

# Security Note

Before publishing screenshots or documentation, sensitive information should be hidden.

Do not expose:

- Tenant IDs
- Subscription IDs
- Private emails
- Machine names
- Usernames
- API keys
- Passwords
- Access tokens
