# Notes – Ticket 01: Network Adapter Failure

## Issue Overview

This replicated a practical IT scenario: a VM could not access the internet because its virtual network adapter was disabled. The exercise reinforced checking both the guest OS and the host/virtualisation layer early in troubleshooting.

---

## Environment

- **Virtualisation Tool:** VirtualBox 7.1.6  
- **Guest OS:** Ubuntu 22.04 LTS  
- **Host OS:** Windows 11 (24H2)  
- **VM Network Mode:** Bridged Adapter  
- **Account Used:** `jordan-bradfield` (non-root)

---

## Symptoms Observed

- Network icon missing in the Ubuntu GUI  
- `ping google.com` → `Temporary failure in name resolution`  
- No IP assigned to the interface (`ip a` showed only loopback)  

These symptoms indicated the VM had no active network interface.

---

## Key Diagnostic Commands and Purpose

1. Check IP address and interfaces  
    ip a  
   - Shows all interfaces and assigned IPs. Confirmed only loopback was present.

2. Test DNS resolution and basic connectivity  
    ping google.com  
   - Tests both reachability and DNS resolution. Failure suggested no network or DNS.

3. Check the network manager service status  
    systemctl status NetworkManager  
   - Verifies NetworkManager is running; useful if the adapter is present but not managed.

4. Inspect NetworkManager connections  
    nmcli connection show  
   - Lists configured connections and their state; helps identify inactive profiles.

5. Test DNS directly (bypass local resolver)  
    dig @8.8.8.8 google.com  
   - Confirms whether DNS resolution works externally, isolating DNS vs routing issues.

---

## Root Cause

VirtualBox **Adapter 1** was disabled in the VM settings. The guest OS could not detect a NIC and therefore could not obtain a DHCP lease or access the network.

---

## Fix Applied

1. Powered off the Ubuntu VM.  
2. Opened VirtualBox → **Settings → Network**.  
3. Enabled **Adapter 1**.  
4. Set the adapter to **Bridged Adapter** and selected the host Ethernet NIC.  
5. Booted the VM and re-tested connectivity.

---

## Verifying the Fix

1. Confirm IP assignment  
    ip a  
   - A valid DHCP IP (e.g., `192.168.x.x`) appeared on the VM interface.

2. Confirm external connectivity and DNS  
    ping google.com  
   - Successful replies confirmed both network and DNS resolution.

---

## Escalation Paths (if the above fix had not worked)

- Restart networking services on the guest:  
    sudo systemctl restart NetworkManager

- Inspect recent NetworkManager logs for clues:  
    journalctl -u NetworkManager --since "10 minutes ago"

- Test an alternate adapter mode to isolate host vs bridged issues: switch VM network to NAT and re-test.

- Verify the host NIC and firewall/AV settings: ensure the host interface is up and not blocked by security software.

---

## Additional Troubleshooting Tips

- Power off the VM before changing VirtualBox network settings to avoid inconsistent states.  
- Bridged mode requires a physically connected host NIC; if the host is on Wi-Fi or disconnected, consider NAT for testing.  
- Temporarily disable host firewall/antivirus if you suspect VM traffic is being blocked.

---

## Broader Application

The same principle applies across hypervisors (VMware, Hyper-V, Proxmox): always include the virtualisation layer in your checklist. Physical machines show similar symptoms when network adapters or drivers are disabled.

---

## Key Takeaways

- Begin with simple, layered checks (`ip a`, `ping`) to quickly narrow the problem.  
- Include virtualization/host checks early when the issue affects a VM.  
- Use `nmcli`, `systemctl`, and `dig` to broaden diagnostic coverage beyond basic pings.  
- Prepare escalation steps to show depth of troubleshooting if the obvious fix fails.

---

## Screenshot References

| Description                        | Image Path                                |  
|------------------------------------|--------------------------------------------|  
| Disabled adapter in VirtualBox     | ![](../images/network-disabled.png)        |  
| Ubuntu with no connection          | ![](../images/ubuntu-no-network.png)       |  
| Ping failure                       | ![](../images/ping-failure.png)            |  
| Enabled adapter in VirtualBox      | ![](../images/network-enabled.png)         |  
| Successful DHCP IP assignment      | ![](../images/ip-a-success.png)            |  
| Ping success (after fix)           | ![](../images/ping-success.png)            |

---

## Final Thoughts & Forward Link

This was a small but realistic issue, disabled adapters are easy to overlook. The exercise reinforced the value of methodical checks across OS and virtualisation layers.

In the next ticket I’ll expand into **firewall rules**: simulating blocked traffic (e.g., HTTPS blocked by pfSense) and demonstrating log-based troubleshooting and rule remediation.
