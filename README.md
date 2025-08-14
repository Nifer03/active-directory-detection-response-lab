Active Directory Detection & Response Lab (SOAR Integration)

## 🎯Overview


This lab demonstrates the design and implementation of an end-to-end incident detection and automated response workflow for Active Directory environments. It simulates a SOC analyst’s role in identifying suspicious authentication activity, triaging the incident, and triggering automated account containment using Splunk, Shuffle SOAR, Slack, and Active Directory.

This project was built from scratch, integrating SIEM detection with SOAR automation in a virtualized enterprise environment.


## 📌Lab Sections


1. [Overview](#overview)
2. [Requirements](#requirements)
3. [Diagram](#diagram)
4. [Architecture](#architecture)
5. [Setup Instructions](#setup-instructions)
6. [Hands-On Scenarios](#hands-on-scenarios)
7. [Future Enhancements](#future-enhancements)
8. [Skills Practiced](#skills-practiced)


## 🔌Requirements


**Cloud & Infrastructure**

Vultr account (or equivalent cloud provider)

Windows Server 2022 ISO

Ubuntu 22.04 ISO

**Software & Tools**

Splunk (Enterprise trial)

Shuffle SOAR account

**Alerting & Communication**

Slack workspace for alerts

Email account for notifications


## 📋Diagram

<p align="center">
  <img src="images/ad-lab-diagram.png" alt="Active Directory SOAR Lab Diagram" width="600">
</p>

**Description:**
This diagram illustrates the Active Directory Detection & Response Lab. It shows how authentication events from the Windows test server are sent to Splunk SIEM, which detects suspicious logins (Event ID 4624, Logon_Type=7) and triggers Shuffle SOAR playbooks. Depending on the analyst’s decision, SOAR can automate actions in Active Directory (e.g., disabling a user) and notify the SOC via Slack or email.


## 🔖Architecture

**Components:**

1. Active Directory Domain Controller (Windows Server, IP: 149.23.151.133)

  Hosts user accounts and manages authentication.

  Receives SOAR commands (e.g., disable user).

2. Windows Test Server (IP: 201.147.112.42)

  Domain-joined endpoint generating login events (Event ID 4624, Logon_Type=7).

3. Splunk SIEM (Ubuntu, IP: 45.72.31.163)

  Collects Windows Event Logs via Universal Forwarder.

  Detection Rule (SPL): 

index="nifer-ad" EventCode=4624 (Logon_Type=7 OR Logon_Type=10)
Source_Network_Address=* Source_Network_Address!='-' Source_Network_Address!=40.* 
| stats count by _time,ComputerName,Source_Network_Address,user,Logon_Type

4. Shuffle SOAR
   
Orchestrates the automated response workflow

5. Attacker Machine

Simulates unauthorized login attempts (RDP using CSmith account).

**Data Flow:**

⬇️ Test user performs a login (normal or suspicious)

⬇️ Domain Controller logs authentication activity (Event ID 4624)

⬇️ Logs are forwarded to Splunk via Universal Forwarder

⬇️ Splunk detects suspicious logins using an SPL rule

⬇️ Detection triggers Shuffle SOAR via webhook

⬇️ Shuffle sends an analyst notification to Slack and email

⬇️ Analyst reviews and approves or denies containment

If approved, Shuffle disables the user account in Active Directory

**Note:** The IP addresses are placeholders.


## 🧰Setup Instructions

1. **Lab Environment Setup**

Create Vultr Instances. Open a Vultr account and provision three instances with the following specifications:

| Instance              | vCPUs | RAM     | Storage    | OS                           | Purpose                                      |
| --------------------- | ----- | ------- | ---------- | ---------------------------- | -------------------------------------------- |
| AD Machine            | 2     | 4096 MB | 80 GB SSD  | Windows Server 2022 Standard | Domain Controller, hosts user accounts       |
| Test Machine          | 1     | 2048 MB | 55 GB SSD  | Windows Server 2022 Standard | Domain-joined endpoint for login simulations |
| Splunk + Shuffle SOAR | 4     | 8192 MB | 160 GB SSD | Ubuntu 22.04 x64             | SIEM and SOAR platform                       |

**Configure Networking**

Set up the VPC network to ensure all instances can communicate securely.

Configure firewall rules to allow necessary ports:

RDP for Windows instances (port 3389)

SSH for Ubuntu (port 22)

Splunk web interface (default port 8000)

Shuffle SOAR web interface and API ports 

**2. Active Directory Setup**

**Configure Domain Controller**

Set up the AD machine as a Domain Controller for the domain nifer.local.

Create a test user account (e.g., CSmith).

**Join Test Machine to Domain**

Add the Windows Test Machine to the nifer.local domain.

Authenticate using the CSmith account to verify domain membership.

**Enable Remote Desktop for Test User**

Configure RDP permissions for test account (CSmith) to simulate lateral movement attempts

Test RDP login using the test account to ensure access is working.

**3. Splunk SIEM Setup**

**Install Splunk Enterprise**

Install and configure Splunk Enterprise on the Ubuntu server.

**Forward Windows Logs**

Install the Splunk Universal Forwarder on both the Windows Test Server and the AD Domain Controller.

Configure them to forward security/event logs to the Splunk instance.

**Create Splunk Alert** 

Set up a scheduled search to detect suspicious logins:

Event ID: 4624

Logon_Type: 7 (remote interactive) or 10 (remote desktop/console)

Configure the alert to trigger when matching events occur.

**4. Shuffle SOAR & Slack Integration**

Create accounts for Shuffle SOAR and Slack.

Connect Splunk’s webhook to Shuffle SOAR to trigger automation workflows.

In Slack, create a workspace and channel (e.g., #alerts), and retrieve the Channel ID.

Authenticate the Shuffle SOAR Bot with Slack.

Build a workflow that:

Detects Event ID 4624 (Logon_Type 7/10) from Splunk alerts.

Sends a Slack alert for suspicious login activity.

Sends an email to the analyst with an approval link to authorize or deny account disablement.

If approved, queries Active Directory via LDAP to locate the targeted account.

Disables the account in Active Directory.

Sends a final confirmation alert to Slack once the account has been disabled.


## 💻Hands-On Scenarios


Incident Response Workflow – Unauthorized Login Containment

**Step 1 – Detection (Splunk Alert)**

Splunk detects a successful Windows logon (Event ID 4624) from the test server.

![Splunk Alert Screenshot](images/Splunk-alert-1.png)


**Step 2 – Alert Forwarding (SOAR Webhook)**

Splunk forwards the event to Shuffle SOAR via webhook for automated triage.

![SOAR Webhook Screenshot](images/webhook-alert.png)


**Step 3 – Automated Investigation (Playbook Execution)**

Shuffle queries Active Directory for the affected account and triggers an approval workflow for the SOC analyst.

![SOAR Playbook Screenshot](images/AD-alert.png)


**Step 4 – Analyst Decision (Email Approval Workflow)**

SOC analyst receives an email with action links (Approve/Deny) to authorize account disablement.

![Email Notification Screenshot](images/Would-you-like-to-disable-the-user.png)

**Step 5 – Response Notification (Slack Update)**

Slack posts an alert in the SOC channel summarizing the login event, the analyst’s decision, and containment status.

![Slack Notification Screenshot](images/User-has-been-disabled.png)

**Step 6 – Containment Confirmation (Active Directory)**

Active Directory confirms the account has been disabled following analyst approval

![AD Disabled Confirmation Screenshot](images/User-Disabled-Confirmation.png)


## 🔭Future Enhancements

1. Convert epoch timestamp to human-readable format in notifications for faster incident analysis.

2. Include the Source Network Address in Slack alerts to provide immediate context on login origin.

3. Add geolocation-based anomaly detection to flag logins from unusual or high-risk locations.

4. Automate password reset after account disablement to further secure compromised credentials.


## 🔓Skills Practiced

1. SIEM alert configuration (Splunk)

2. SOAR automation & playbook design (Shuffle SOAR)

3. Active Directory querying and account management (Windows Server)

4. Incident response workflow creation with analyst approval logic

5. Integration of Slack and email notifications into security workflows

6. Event log analysis (Windows Security Event ID 4624)



