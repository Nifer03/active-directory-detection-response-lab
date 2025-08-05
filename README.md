Active Directory Detection & Response Lab (SOAR Integration)

This project simulates a real-world enterprise detection and response environment using Windows Server and Linux systems hosted on **Vultr Cloud**. The goal is to detect a successful attacker authentication, generate alerts, and automate incident response.

Platform

All servers are deployed on Vultr using a mix of Windows Server 2022 and Ubuntu 22.04 instances.

Lab Architecture

Server 1: Windows Server 2022 — Domain Controller (example.local); Hostname: Nifer-ADDC01
Server 2: Ubuntu 22.04 — Splunk + Shuffle SOAR
Server 3: Windows Server 2022 — Domain-joined test machine
Attacker: Simulated login activity targeting Server 3
- Alerting: Slack + Email
- Automation: Shuffle SOAR disables users in Active Directory (via Server 1)

Workflow

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

