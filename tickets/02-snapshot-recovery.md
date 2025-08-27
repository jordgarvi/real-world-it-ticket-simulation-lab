# Ticket 02 – Snapshot Recovery

## Incident Logging
- **Ticket ID:** 0002-SR  
- **Date/Time Reported:** 01-07-2025, 21:15 GMT  
- **Reported by:** Internal user *jordanb*  
- **Channel:** Internal IT support request  

---

## Categorisation & Priority
- **Category:** Virtualization / System Recovery  
- **Impact:** Single VM (Ubuntu 22.04)  
- **Urgency:** High (system unstable and unbootable)  
- **Priority:** P2 (High)  

---

## Issue Summary
After applying updates and configuration changes, the Ubuntu VM failed to boot consistently and showed GRUB and pre-startup script errors. A snapshot recovery was required to return the VM to a known good state.  

---

## Environment
- VirtualBox 7.1.6  
- Ubuntu 22.04 LTS  
- Host OS: Windows 11 (24H2)  
- Snapshot: **Ubuntu Clean Install**  
- Snapshot Date: 29-06-2025  

---

## Symptoms
- VM failed to boot, showing GRUB error messages.  
- Inconsistent behavior: sometimes booted to desktop but with warning message referencing a pre-startup script.  
- User unable to confirm stability or reliability.  

| Description                     | Image                                 |
|---------------------------------|----------------------------------------|
| Snapshot before issue           | ![](../images/snapshot-before.png)     |
| GRUB boot failure               | ![](../images/grub-boot-error.png)     |
| Pre-script warning during boot  | ![](../images/pre-script-error.png)    |

---

## Investigation & Diagnosis
- Restored snapshot “Ubuntu Clean Install” from VirtualBox.  
- System entered **emergency mode**, confirming the snapshot had been taken *after* the problematic changes.  
- Boot log (`/var/log/boot.log`) reviewed: `[FAILED]` units identified, confirming degraded system state.  

| Description                    | Image                                     |
|--------------------------------|--------------------------------------------|
| Boot log with failures         | ![](../images/snapshot-troubleshoot.png)   |

**Root Cause:**  
Snapshot was captured *after* unstable updates/configuration changes, preserving the problematic state.  

---

## Resolution & Recovery
1. Powered off the VM.  
2. Restored snapshot via **VirtualBox → Snapshots tab**.  
3. Booted VM and verified state.  
4. Performed log review (`sudo less /var/log/boot.log`) to confirm issues.  
5. Conducted network and connectivity tests post-restore.  

| Description                    | Image                                         |
|--------------------------------|------------------------------------------------|
| Snapshot list before restore   | ![](../images/snapshot-restore-before.png)     |
| Restore confirmation dialog    | ![](../images/snapshot-restore-confirm.png)    |
| Ping test after restore        | ![](../images/ping-success-after-restore.png)  |
| IP assigned after restore      | ![](../images/ip-a-after-restore.png)          |

---

## Verification
- VM booted successfully to Ubuntu desktop.  
- `ping google.com` succeeded ✅  
- `ip a` showed DHCP-assigned IP address ✅  
- User confirmed system stability and functionality.  

---

## Closure
- Ticket status set to *Resolved*.  
- Confirmed recovery with reporting user prior to closure.  

---

## Lessons Learned
- Snapshots must be carefully timed, ensure snapshots are taken *before* major changes, not after.  
- Inconsistent boot behavior highlights the need for log review, not just surface-level checks.  
- Importance of snapshot strategy: regular, well-timed snapshots enable quick recovery and reduce downtime.  

---
