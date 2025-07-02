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
| Pre-script warning during boot  | ![](../images/pre-script-error.png)   |

The user requested IT review and confirmation that the system was stable, and recovery steps were advised to ensure a clean working state.

## Snapshot Recovery Steps

The VM was powered off, and the previously created snapshot ("Ubuntu Clean Install") was restored using the VirtualBox Snapshots tab.

| Description                  | Image                                         |
|------------------------------|-----------------------------------------------|
| Snapshot list before restore | ![](../images/snapshot-restore-before.png)    |
| Restore confirmation dialog  | ![](../images/snapshot-restore-confirm.png)   |

After restoring the snapshot, the VM booted — but returned to the previously broken state, entering **emergency mode** as expected.

> ✅ This confirms that the snapshot captured the VM *after* the problematic changes were made, validating both the timing of the snapshot and the recovery process.

## Initial Troubleshooting

After restoring the snapshot, the system booted but still displayed instability. To begin diagnostics, `boot.log` was reviewed for any critical failures during startup.

### Steps Taken:

1. Booted the VM into the current (problematic) state.
2. Accessed the terminal using `Ctrl+Alt+F2` if necessary.
3. Ran the following command to inspect boot messages:

sudo less /var/log/boot.log

4. Scanned for `[FAILED]` units or other error messages.
5. Captured screenshot showing logged errors.

| Description              | Image                                       |
|--------------------------|---------------------------------------------|
| Boot log with failures   | ![](../images/snapshot-troubleshoot.png)   |

> 🧠 The log output confirmed the system was in a degraded state, supporting the need to fully restore to a known good configuration prior to the problematic changes.

## Verification

Once the snapshot was restored and boot log reviewed, the VM eventually booted normally again. The user confirmed desktop access and tested network functionality.

Terminal checks were run to confirm full recovery:

ping google.com
ip a

| Description              | Image                                           |
|--------------------------|-------------------------------------------------|
| Ping test after restore  | ![](../images/ping-success-after-restore.png)  |
| IP assigned after restore| ![](../images/ip-a-after-restore.png)          |

> ✅ These results confirmed that the VM had returned to a stable, functioning state following the snapshot restoration.
