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
