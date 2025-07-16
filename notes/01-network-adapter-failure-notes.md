# Notes – Ticket 01: Network Adapter Failure

## Issue Overview

This issue simulated a **common real-world problem**: a VM not connecting to the internet due to a disabled network adapter. It reinforced the importance of verifying *both* the guest OS and host virtualization platform when troubleshooting connectivity.

---

## Environment

- **Virtualization Tool**: VirtualBox 7.1.6  
- **Guest OS**: Ubuntu 22.04 LTS  
- **Host OS**: Windows 11 (24H2)  
- **VM Network Mode**: Bridged Adapter  
- **Account Used**: `jordan-bradfield` (non-root)

---

## Symptoms Observed

- Missing network icon in Ubuntu GUI  
- `ping google.com` returned: `Temporary failure in name resolution`  
- No IP assigned to the interface (`ip a` showed only loopback)  

These symptoms clearly suggested **no active network interface** on the VM.

---

## Key Diagnostic Commands and Their Purpose

### 1. Check IP address:  
```bash
ip a
```

- **What it does:** Shows all network interfaces and their IP addresses on the system.  
- **Why we ran it:** To check if the VM had an active network interface with a valid IP address. Seeing only the loopback interface means no network was assigned.

### 2. Test DNS resolution and connectivity:  
```bash
ping google.com
```

- **What it does:** Sends ICMP echo requests (“pings”) to `google.com` to test network connectivity and DNS resolution.  
- **Why we ran it:** To verify if the VM can reach external hosts and resolve domain names. The failure indicated either no network or broken DNS.

---

## Root Cause

VirtualBox **Adapter 1** was disabled in VM settings. Since the guest OS couldn’t detect a physical NIC, it couldn’t obtain an IP address or connect to the internet.

---

## Fix Applied

1. **Powered off** the Ubuntu VM.  
2. Opened VirtualBox → **Settings → Network**  
3. Enabled **Adapter 1**.  
4. Set the attached mode to **Bridged Adapter**.  
5. Selected the correct host NIC (Ethernet).  
6. Booted VM and tested connectivity again.

---

## Verifying the Fix

### 1. Check IP after fix:  
```bash
ip a
```

- The output now shows a valid IP address (e.g., `192.168.x.x`) assigned to the VM’s network interface (`enp0s3`). This confirms the VM is connected to the network.

### 2. Confirm internet connectivity:  
```bash
ping google.com
```

- Successful ping responses confirm the VM can resolve domain names and communicate with external servers.

---

## Screenshot References

| Description                        | Image Path                                |  
|------------------------------------|--------------------------------------------|  
| Disabled adapter in VirtualBox     | ![](../images/network-disabled.png)     |  
| Ubuntu with no connection          | ![](../images/ubuntu-no-network.png)    |  
| Ping failure                       | ![](../images/ping-failure.png)         |  
| Enabled adapter in VirtualBox      | ![](../images/network-enabled.png)      |  
| Successful DHCP IP assignment      | ![](../images/ip-a-success.png)         |  
| Ping success (after fix)           | ![](../images/ping-success.png)         |

---

## Final Thoughts

This was a small but realistic scenario — a disabled network adapter is easy to overlook. It helped reinforce a key principle:  
> **Always check the virtualization layer when network problems arise in a VM.**

Using simple commands like `ip a` and `ping` in a logical order helped quickly pinpoint the issue. Documenting the process ensures I can easily repeat the troubleshooting if needed.

---
