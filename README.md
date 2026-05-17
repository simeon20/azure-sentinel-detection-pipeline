# azure-sentinel-detection-pipeline
Built an end-to-end Microsoft Sentinel detection pipeline: onboarded a Linux VM via AMA (DCR) to ingest Syslog, enabled Azure Activity via policy, wrote KQL parsers, created a Scheduled Analytics (SSH brute force &amp; Azure delete), verified incidents in Defender, and built a workbook dashboard. Includes JSON exports &amp; KQL queries.

---------
Azure Sentinel Mini-SOC (Azure Activity + Linux Syslog)

End-to-end detection pipeline built on Microsoft Sentinel:

Onboarded a Linux VM (Ubuntu) via Azure Monitor Agent (AMA) using a Data Collection Rule (DCR) to ingest Syslog

Connected Azure Activity logs to the workspace using Azure Policy (no agents needed)

Authored KQL to normalize sshd failures (parse username & source IP)

Created a Scheduled Analytics rule

SSH Brute Force (Syslog)

Built a Workbook dashboard (Top IPs, Timeline, Account+IP) and pinned tiles to the Azure Portal dashboard

Packaged KQL and exported Analytics/Workbook JSON for version control

Portal note: This README uses the new Defender (unified) navigation. When pinning charts/tables, they go to your Azure Portal dashboard, not a Defender-only dashboard.

 ![Over Time Screenshot](FailedSSHAttemptsOverTime.png)
 ![attempts per user Screenshot](Showattemptsperuser.png)
 ![Top Source IPs Screenshot](TopSourceIPs(failedSSH).png)

---

Architecture:

Azure Activity (subscription) → Export Activity Logs → Log Analytics Workspace (LAW) ⇄ Microsoft Sentinel
Linux VM → AMA + DCR (Syslog) → LAW ⇄ Microsoft Sentinel
Sentinel → Analytics Rules → Alerts → Incidents
Sentinel/Monitor → Workbook → Azure Portal Dashboard (pinned tiles)

----

Prerequisites:

Azure subscription with permissions

Owner on subscription (to export Activity Logs)

Contributor on resource group(s)

Microsoft Sentinel Contributor on the workspace

A Log Analytics Workspace (e.g., law-sentinel-demo) with Microsoft Sentinel enabled

One Linux VM (Ubuntu) with outbound internet and a public IP (for demo)

Entra ID P1/P2 for Sign-in logs

---

Stop log ingestion to save money:

Subscription Activity Logs

Azure Portal → Subscriptions → your sub → Activity log → Export Activity Logs / Diagnostic settings → Delete the setting that sends to your workspace.

(If you used Policy) Azure Portal → Policy → Assignments → find “Configure Azure Activity logs to stream…” → Delete assignment (also deletes the system-assigned MI it created).

Entra ID (Azure AD) logs (if you enabled them)

Entra admin center → Monitoring → Diagnostic settings → Delete any settings that send Sign-in/Audit logs to your workspace.

Linux Syslog

Azure Portal → Monitor → Data Collection Rules → open your DCR → Delete it (or remove the VM from Resources).
This stops AMA from shipping logs. The AMA extension can stay—it doesn’t cost by itself.

Shut down/lock down the VM

If you want to keep it: Stop (deallocate) the VM and remove the Public IP (Public IPs incur a small hourly charge).

If you don’t need it: Delete the VM. Confirm the NIC, Public IP, Disk, NSG, and VNet are gone (or delete the whole RG that holds them).

Disable detections (optional)

Microsoft Sentinel → Analytics → select your custom rules → Disable.
(Rules themselves aren’t billed, but disabling avoids confusion.)

Cap retention / daily cap (prevents storage charges)

Log Analytics workspace → Usage and estimated costs →

Data retention: set to 7–30 days (shorter = cheaper).

Daily cap: set a small cap (e.g., 0.1 GB) or Off if ingestion is fully disabled.

(Optional) Unpublish shared Dashboard

Azure Portal → Dashboard → Share → Unpublish (if you published it). Private dashboards are free.



