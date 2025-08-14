**Active Directory Detection & Response Lab (SOAR Integration)**

📝**Overview:**
This lab demonstrates the integration of Active Directory (AD) authentication logs into a SOAR workflow to automate security actions, such as disabling suspicious accounts. It simulates a real-world SOC environment where a SIEM detects specific Windows login events and triggers a SOAR playbook in response. The setup is designed to showcase how SIEM, AD, and SOAR tools can work together for efficient incident detection and automated response.

📌**Lab Sections:**
1. Requirements
2. Diagram
3. Architecture
4. Workflow
5. Hands-On Scenarios
6. Future Enhancements
7. Skills Practiced

⚙️**Requirements**

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

🖼️**Diagram**

<p align="center">
  <img src="images/ad-lab-diagram.png" alt="Active Directory SOAR Lab Diagram" width="600">
</p>

**Description:**
This diagram illustrates the Active Directory Detection & Response Lab. It shows the flow of authentication events from a Windows test server (IP: 201.147.112.42) to the Splunk SIEM (IP: 45.72.31.163), which detects successful login events (Event ID 4624, Logon_Type=7) and triggers Shuffle SOAR playbooks. Depending on the analyst’s decision, SOAR can then automate actions in Active Directory (e.g., disabling a user) and notify the SOC via Slack or email.

**Note:** The IP addresses are placeholders and not real.

🏗️**Architecture**

This lab integrates multiple components to simulate a SOC environment and automate responses to suspicious logins.

1. Active Directory Domain Controller (Windows Server)- Label as Nifer-ADDC01 IP: 149.23.151.133

Shows where user accounts live and actions (disable account) happen

2. Windows Test Server (IP: 201.147.112.42)

Domain-joined endpoint

Generates login events (Event ID 4624, Logon_Type=7)

3. Splunk SIEM (Ubuntu) (IP: 45.72.31.163)

Collects and analyzes Windows Event Logs 

Detects suspicious logins (Event ID 4624, Logon_Type 7 & 10) With SPL: index="nifer-ad" EventCode=4624 (Logon_Type=7 OR Logon_Type=10) Source_Network_Address=* Source_Network_Address!='-' Source_Network_Address!=40.* |stats count by _time,ComputerName,Source_Network_Address,user,Logon_Type

Shuffle SOAR (Ubuntu)

Receives alerts via webhook from Splunk

Executes playbooks (optiom to disable AD user, notify SOC via Slack/email)

Attacker Machine (I RDP via my AD machine using CSmith's account who is under my AD domain)

Simulates unauthorized login attempts

Alerting Channels 

Slack and Email notifications to SOC analysts














Data Flow:

Active Directory generates authentication logs (e.g., Event ID 4624 for successful logons).

Windows Event Forwarding sends these logs to the Splunk SIEM.

Splunk runs a scheduled search to detect specific conditions (e.g., unexpected RDP logons).

Upon a match, Splunk sends a webhook to the Shuffle SOAR platform.

Shuffle SOAR runs a playbook that queries Active Directory for user details and can optionally disable the account.

Example alert data from Splunk

1. Attacker performs a successful login to the test server.
2. Splunk (on Ubuntu) detects the authentication event.
3. Shuffle triggers:
   - A Slack alert
   - An email to the SOC analyst
   - A playbook to disable the user in Active Directory (if approved)

