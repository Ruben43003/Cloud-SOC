# Building a SOC + Honeynet in Azure (Live Traffic)
![Cloud Honeynet / SOC](https://i.imgur.com/ZWxe03e.jpg)

## Introduction

In this project, I constructed a honeynet in Azure and ingested log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the extremely insecure environment for 24 hours, applied some security controls to harden the environment, measured metrics for another 24 hours, then showed the results below. The following metrics are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources that were originally deployed, were exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/yzF0939.png)<br>
![Linux Syslog Auth Failures](https://i.imgur.com/jLPXCW1.png)<br>
![Windows RDP/SMB Auth Failures](https://i.imgur.com/n2Cv4Mz.png)<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in the insecure environment for 24 hours:
Start Time 2023-05-01T00:14:15
Stop Time 2023-05-02T00:14:15

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 8196
| Syslog                   | 17211
| SecurityAlert            | 16
| SecurityIncident         | 249
| AzureNetworkAnalytics_CL | 437

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in the environment for another 24 hours, but after we have applied security controls:
Start Time 2023-05-02T15:55:05
Stop Time	2023-05-03T15:55:05

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 5603
| Syslog                   | 24
| SecurityAlert            | 0
| SecurityIncident         | 85
| AzureNetworkAnalytics_CL | 0

## Real-World Scenario "The malicious employee"
![Attacker image](https://i.imgur.com/PAvV6nX.jpg)<br>

While constructing the Azure CLoud and developing KQL queries I though it would be helpful to test out a possible real world scenario, create a KQL to detect the threat, and explain how I as a Security Proffessional would respond. 

Scenario: Mr.Robinson is the head of the sales department at the prestigous law firm "Nelson and Murdock". It is an unfortunate tuesday afternoon and the company has decided that employee John Doe will be fired next week, as he has not been up to par with company policy. An employee who was present during the meeting also happens to be a friend of John's and informs him . John is furious and researches ways to get his revenge. The next day John waits for Mr.Robinson to go out for his daily coffee run at 10am. Mr.Robinson leaves his desktop unlocked and walks out of his office to go grab coffee which will take him a total of about 20 minutes. John sneaks into the office and disables Mr.Robsinsons firewall which will allow ICMP traffic, and allow John to "ping of death" Mr.Robinsons device later that night. 

Response utilizing NIST 800-61: 

Preperation: Using the guidelines of NIST 800-61 Incident response I have already thought about the threat before it occured as I followed the preparation phase and use my existing knowledge of security threats to "think like a bad guy:. I have an active query which spins up a high threat security incident whenever a users firewalls are turned on/off. 

Indentifcation and Containment/Eradication: I would recieve the alert on the SIEM solution in place and immediatley react by remoting into MR.Robinsons device and locking it. I would then follow this by removing his device off the network and inspecting if there was any malware or data breach on his device. Once I have verified that it was only the firewall turned off I simply turn it back on and check the cameras for the time that the incident occured. Here I am able to confirm the attacker was in fact John Doe and my supervisors as well as the authorities are informed immediatley. 

Post incident response: Following the post incident analysis I make sure that 2 very important issues are solved. Issue #1 being that a user was able to turn off a firewall without an admin password, serves as an extreme security risk. Therefore all devices will be revised and hardened so that any administrator level actions now require and administrator password. The second and most important issue that is rarley reviewd  is End User training. In this scenario Mr.Robinson left his device wide open and for 25 minutes. The companies users will have to attend mandatory security training that will emphasize on the importance of keeping your device secure where we as security proffesionals cannot. 



## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
