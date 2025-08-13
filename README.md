**Active Directory Detection & Response Lab (SOAR Integration)**

**Overview:**
This lab demonstrates the integration of Active Directory (AD) authentication logs into a SOAR workflow to automate security actions, such as disabling suspicious accounts. It simulates a real-world SOC environment where a SIEM detects specific Windows login events and triggers a SOAR playbook in response. The setup is designed to showcase how SIEM, AD, and SOAR tools can work together for efficient incident detection and automated response.

**Lab Sections:**
1. Requirements
2. Diagram
3. Architecture
4. Workflow
5. Hands-On Scenarios
6. Future Enhancements
7. Skills Practiced

**Requirements**

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

**Diagram**




1. Attacker performs a successful login to the test server.
2. Splunk (on Ubuntu) detects the authentication event.
3. Shuffle triggers:
   - A Slack alert
   - An email to the SOC analyst
   - A playbook to disable the user in Active Directory (if approved)
---
Manage our Firewall Group

Change SSH source from Anywhere to My IP

Add MS RDP and change source from Anywhere to My IP

Connect via RDP from my desktop to my ADDC01

Open command prompt and type ipconfig and check our IP add

Go on Firewall, click dropdown select AD project. Nobody can connect to my VM except me.

Go to Test Machine server and click View Console, perform ipconfig

After this, go to settings and enable VPC Network so that all my VMs within the same VPC can communicate with each other internally.

Enable VPC for ADDC01 as well

Go to PowerShell and connect to our Splunk server via SSH

Enable VPC for Splunk and configure firewall.

Once I go back to Powershell, I got disconnected after configuring my firewall.

Go to Powershell again and type ip a. Now I have another network interface as matched in NPC

Ping both my test machine and ADDC, Destination Host Unreachable

I'm gonna find out why I'm getting Host Unreachable. RDP our test machine and ipconfig.

2nd network interface is different from my machine's private IP. Go to Network Settings. CLick Ethernet Instance 02

Double click and select IpV4. Select Use the following IP Address.

Edit IP address and subnet mask based on my server's information in my vltur account.

Now let's ping my splunk server and test machine to see if I'm getting a connection. 

And perfect! They are now talking to each other.

