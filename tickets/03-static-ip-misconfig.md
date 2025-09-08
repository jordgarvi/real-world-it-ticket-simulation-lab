# Ticket 03 – Static IP Misconfiguration

## Incident Logging
- Ticket ID: 0003-SIP
- Date/Time Reported: 08-07-2025, 16:33 GMT
- Reported by: Internal user "jordanb"
- Channel: Internal IT support request

---

## Categorisation & Priority
- Category: Network / Connectivity
- Impact: Single VM (Ubuntu)
- Urgency: High (complete loss of connectivity)
- Priority: P2 (High)

---

## Issue Summary
After manually assigning a static IP to the Ubuntu VM’s network interface using NetworkManager, the system lost internet connectivity. The user was unable to reach external IPs and domains, indicating a misconfiguration of network parameters.

---

## Environment
- VirtualBox 7.1.6
- Ubuntu 22.04 LTS
- Host OS: Windows 11 Version 24H2
- Network Interface: netplan-enp0s3

---

## Symptoms
- Loss of internet connectivity after static IP assignment
- ping -c 3 8.8.8.8 and ping -c 3 google.com failed
- Routing table missing correct default gateway
- DNS resolution not functional
- IP assigned but no external connectivity

---

## Investigation & Diagnosis
### Commands Used
ip a
ip route
nmcli connection show "netplan-enp0s3"
cat /etc/netplan/*.yaml
cat /etc/resolv.conf

### Observations
- IP address manually set but gateway incorrect or missing
- Routing table showed no path to external networks
- DNS resolution failed
- Netplan YAML misconfigured
- NetworkManager settings applied but invalid

### Escalation Considerations (If Initial Fix Failed)
- Manually add a default gateway: `sudo ip route add default via <gateway-ip>`
- Restart network services: `sudo systemctl restart NetworkManager`
- Check deeper logs: `journalctl -u NetworkManager`
- Test DNS more directly: `dig @8.8.8.8 google.com`
- Use `traceroute 8.8.8.8` to see where packets drop

### Screenshots
| Description | Screenshot |
|-------------|------------|
| ip a output | ![](../images/ip-a-investigation.png) |
| ip route output | ![](../images/ip-route-investigation.png) |
| Netplan YAML | ![](../images/netplan-yaml.png) |
| nmcli connection show | ![](../images/nmcli-show.png) |
| resolv.conf | ![](../images/resolv-conf.png) |

> Root cause confirmed: static IP misconfiguration blocking routing and DNS

---

## Resolution & Recovery

### Fix Applied

Reverted the network interface to DHCP using NetworkManager. This ensures the system obtains a working dynamic IP configuration:

nmcli connection modify "netplan-enp0s3" ipv4.addresses ""
nmcli connection modify "netplan-enp0s3" ipv4.gateway ""
nmcli connection modify "netplan-enp0s3" ipv4.dns ""
nmcli connection modify "netplan-enp0s3" ipv4.method auto
nmcli connection down "netplan-enp0s3"
nmcli connection up "netplan-enp0s3"

### Verification
- ip a shows valid dynamic IP
- ip route confirms correct default gateway
- resolv.conf lists valid DNS
- ping -c 3 8.8.8.8 and ping -c 3 google.com successful

| Description | Screenshot |
|-------------|------------|
| Static IP applied but no connectivity | ![](../images/static-ip-fix.png) |
| DHCP restored | ![](../images/apply-fix.png) |
| Ping successful | ![](../images/successful-8.8.8.8-ping.png) |

---

## Closur
- Ticket status set to Resolved
- User confirmed access restored

---

## Lessons Learned
- Static IP configuration must include IP, gateway, and DNS  
- Misconfigured gateway or NAT can block connectivity  
- Reverting to DHCP quickly confirms root cause  
- Structured ITIL workflow: log, categorise, investigate, resolve, verify, reflect  
- nmcli, ip route, and resolv.conf are essential troubleshooting tools  
- Future prevention: document static configurations clearly and always test them in a controlled way before applying to production or critical systems  

---

## Lessons Learned & Reflection

- Static IP configuration requires **three key elements**: IP, gateway, and DNS. Missing one breaks connectivity.  
- A misconfigured gateway or DNS will silently block traffic, so always validate with `ip route` and `cat /etc/resolv.conf`.  
- Reverting to DHCP is not just a quick fix, it’s a powerful diagnostic step that confirms whether the root cause lies in static configs.  
- Wider command use (`nmcli`, `journalctl`, `traceroute`) adds confidence and demonstrates professional troubleshooting depth.  
- To avoid similar issues in the future:  
  - **Document static network configurations** before applying.  
  - **Test changes in a non-critical VM** to verify stability.  
  - **Keep a fallback DHCP profile** so recovery is immediate if a static setup fails.  

> This ticket reminded me how easily small oversights in networking configs can take down connectivity, and how planning, documentation, and testing are the real safeguards against repeat issues.
  

