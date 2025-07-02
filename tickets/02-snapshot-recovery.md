# Ticket 02 – Snapshot Recovery

## Ticket Source
- **Ticket ID:** 0002-SR (Snapshot Recovery)
- **Date Reported:** 01-07-2025
- **Time Reported:** 21:15 GMT
- **Reported by:** Internal user "jordanb"
- **Received via:** Internal IT support request

## Issue Summary
After applying recent system updates and configuration changes, the Ubuntu VM became unstable and failed to boot properly. The user reports error messages during startup and inability to access services. A snapshot recovery is needed to restore the VM to a known good state.

## Environment
- VirtualBox 7.1.6
- Ubuntu 22.04 LTS
- Host OS: Windows 11 Version 24H2
- Snapshot Name: "Ubuntu Clean Install"
- Snapshot Created Date: 29-06-2025

## Symptoms

Initially, the VM failed to boot and presented multiple GRUB error messages during startup, preventing normal access to the system. The user captured a screenshot at the time showing the failed boot sequence.

However, after leaving the machine powered off overnight, the VM unexpectedly booted to the Ubuntu desktop, though a warning message appeared at the top of the terminal referencing a pre-startup script error.

While the system appeared functional on the surface, the inconsistent boot behavior raised concern about system stability.

| Description                     | Image                                 |
|---------------------------------|----------------------------------------|
| Snapshot before issue           | ![](../images/snapshot-before.png)     |
| GRUB boot failure               | ![](../images/grub-boot-error.png)     |
| Pre-script warning during boot | ![](../images/pre-script-error.png)   |

The user requested IT review and confirmation that the system was stable, and recovery steps were advised to ensure a clean working state.


---

## Next Steps: Initial Troubleshooting

* [ ] Try fixes: check logs, run commands, restart services, update packages  
* [ ] Take screenshot of terminal output showing errors or failed fixes

  * Filename: `snapshot-troubleshoot.png`  
  * Markdown: `![](../images/snapshot-troubleshoot.png)`

* [ ] Write Initial Troubleshooting section including steps and screenshot  
* [ ] Commit with message:  
  `Add initial troubleshooting steps and terminal error logs`
