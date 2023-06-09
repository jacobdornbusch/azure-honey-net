# Creating a SOC + Honeynet in Azure with Live Traffic
![Cloud Honeynet Soc](https://i.imgur.com/KQ2gQ1M.png)

## Introduction

In this project, I built a honeynet within Microsoft Azure, employing the following steps:

- Created a honeynet infrastructure in Microsoft Azure.
- Collected log data from various sources and directed them into a Log Analytics Workspace.
- Utilized Microsoft Sentinel to analyze the log data, generate attack maps, trigger alerts, and create incidents.
- Assessed security metrics in the insecure environment for a 24-hour period.
- Implemented basic security controls to fortify the environment.
- Reassessed the security metrics after another 24 hours following the implementation of the security controls.

The metrics were obtained from the following sources:
- SecurityEvent (Windows Event Logs) 
- Derived from the Windows Virtual Machine and the installed MSSQL Server.
- Syslog (Linux Event Logs) 
- Derived from the Linux Virtual Machine.
- SecurityAlert (Log Analytics Alerts Triggered).
- SecurityIncident (Incidents created by Sentinel).
- AzureNetworkAnalytics_CL (Malicious Flows allowed into the honeynet due to configured NSG rules).

The honeynet architecture in Azure consisted of the following components:
- Virtual Network (VNet).
- Network Security Groups (NSG).
- Virtual Machines (2 Windows and 1 Linux).
- Log Analytics Workspace (LAW).
- Azure Key Vault.
- Azure Storage Account.
- Microsoft Sentinel.

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

Upon initial deployment, all resources were highly vulnerable to the public internet. The Network Security Group (NSG) rules of the Virtual Machines, along with their built-in firewalls, were manually configured to permit unrestricted traffic. Additionally, other resources were deployed with public endpoints that were accessible from the internet, without utilizing Private Endpoints. To capture security incidents like unauthorized and unsuccessful login attempts, Azure's Log Analytics Workspace (LAW) was set up to ingest the corresponding data. 

## Verified VM's Logs Will Be Ingested by Azure LAW
![Law Agents](https://i.imgur.com/5X8QZR8.png)

## Verified Failed Authentication Logs
![Failed Auth Windows VM](https://i.imgur.com/26RgM8I.png)

Azure Sentinel detected a significant number of security incidents, primarily caused by malicious login attempts targeting the Windows and Linux Virtual Machines (VMs) and the MSSQL Server installed on the Windows VM. In Azure, these security incidents can be assigned, investigated, and resolved. Examining these incidents provides additional insights into the nature of the attack and the originating IP address(es). The logs frequently revealed repeated login attempts originating from the same IP addresses, as well as multiple IP addresses associated with a single attack instance and geographical region. The following maps provide a visual representation of some of these source locations. 

## Failed Authentication/Brute Force Incidents
![Incident](https://i.imgur.com/6v9q64D.png)
![Incidents](https://i.imgur.com/rPT19ap.png)
![Incidents](https://i.imgur.com/Taf8oLf.png)

## Attack Maps Before Hardening / Security Controls
![Allowed Inbound Malicious Flows](https://i.imgur.com/TSlKpcK.png)
![Linux Syslog Auth Failures](https://i.imgur.com/RVeHxMU.png)
![Windows RDP/SMB Auth Failures](https://i.imgur.com/xxbPOT5.png)
![MSSQL Auth Failures](https://i.imgur.com/SYHODOi.png)

## Metrics Before Hardening / Security Controls

The following table shows the metrics from the insecure environment measured for 24 hours:
Start Time 2023-06-08 01:28:07
Stop Time 2023-06-09 01:28:07

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 22,495
| Syslog                   | 24,182
| SecurityAlert            | 4
| SecurityIncident         | 434
| AzureNetworkAnalytics_CL | 1,448

Following the outcomes of the open honeynet experiment, measures were taken to enhance the security of the Network Security Groups (NSGs). This involved implementing a stringent policy that blocked ALL traffic, with the exception of traffic originating from my workstation. Specifically, the NSGs were configured to allow communication solely from my IP address. Furthermore, public access was deactivated in the firewalls specific to each resource, such as the Key Vault.

To further bolster security, Microsoft Defender for Cloud was leveraged to implement recommended security controls outlined by NIST (National Institute of Standards and Technology) and Microsoft. This comprehensive solution ensured the enforcement of robust security measures across the network, safeguarding against potential threats and vulnerabilities.

## Implementing MS Recommended Security Controls
![MS Recommendations](https://i.imgur.com/AWgk3be.jpg)

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)

## Attack Maps After Hardening / Security Controls

```_All map queries returned no results due to no instances of malicious activity for the 24 hour period after hardening._```
<!--
## Metrics After Hardening / Security Controls

The following table shows the same metrics for another 24 hours _after_ adding the basic aforementioned security controls to the environment:
Start Time 2023-06-09 13:11:37
Stop Time	2023-06-10 13:11:51

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 10,984
| Syslog                   | 24
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0
-->
## No Malicious Security Incidents Following the Implementation of Proper Controls
![Zero Incidents](https://i.imgur.com/smgU4F2.jpg)

## Conclusion

This project involved the creation of a compact honeynet within Microsoft Azure, with the integration of log sources into a Log Analytics workspace. Microsoft Sentinel was utilized to activate alerts and generate incidents based on the ingested logs. Moreover, security metrics were initially measured in the insecure environment, and then again after implementing security measures. Notably, the implementation of security controls resulted in a significant reduction in the number of security events and incidents, showcasing their efficacy.

It is important to consider that if the network resources experienced heavy utilization from regular users, it is possible that an increased number of security events and alerts would have been observed within the 24-hour period following the implementation of the security controls.
