# Domain Connectivity Investigation

A practical troubleshooting case from an isolated corporate network segment. The issue looked like a simple "No Internet" problem at first, but the actual goal was not public Internet access. The workstation had to reach internal DNS, resolve the corporate domain, and discover a domain controller.

All IP addresses, hostnames, domains, and account names in this repository are anonymized examples.

## Environment

- Windows 10 workstation
- Corporate Active Directory domain
- Isolated internal network segment
- Static IPv4 configuration
- Cisco access switch
- Local user without administrative privileges

## Initial Symptoms

The workstation showed network connectivity, but domain services were unavailable.

The following checks failed:

```cmd
ping corp.local
nslookup corp.local
nltest /dsgetdc:corp.local
```

Example result:

```text
*** Unknown can't find corp.local: No response from server
Getting DC name failed: Status = 1355 ERROR_NO_SUCH_DOMAIN
```

Windows also displayed a network status warning. Since this was an isolated corporate network, the Windows "No Internet" status was not treated as the main problem.

## Problem Workstation Configuration

The network adapter had a static IPv4 configuration:

```text
IPv4 Address: 10.10.40.164
Subnet Mask: 255.255.255.240
Default Gateway: 10.10.40.161
DNS Servers: Not configured
```

The adapter had link, and the default gateway was reachable:

```cmd
ping 10.10.40.161
tracert 10.10.40.161
```

Result: successful replies from the gateway.

## DNS Check

The DNS configuration was checked with:

```cmd
netsh interface ipv4 show dnsservers
```

Result:

```text
Configuration for interface "Ethernet"
Statically Configured DNS Servers: None
```

This explained why the workstation could not resolve the domain name or discover a domain controller.

## Domain Discovery Check

The domain controller lookup failed:

```cmd
nltest /dsgetdc:corp.local
```

Result:

```text
Getting DC name failed: Status = 1355 ERROR_NO_SUCH_DOMAIN
```

This confirmed that the workstation could not locate the domain infrastructure.

## Route and ARP Checks

The routing table was checked:

```cmd
route print
```

The workstation had a default route through its configured gateway:

```text
0.0.0.0/0 -> 10.10.40.161
10.10.40.160/28 -> On-link
```

ARP was also checked:

```cmd
arp -a
```

The workstation could see its local gateway addresses, so the local link was working.

## Comparison with Working Workstations

A working workstation in the same area was checked with:

```cmd
ipconfig /all
```

The working workstation had a different configuration:

| Parameter | Problem Workstation | Working Workstation |
|---|---:|---:|
| IPv4 Address | 10.10.40.164 | 10.10.40.105 |
| Subnet Mask | 255.255.255.240 | 255.255.255.192 |
| Default Gateway | 10.10.40.161 | 10.10.40.65 |
| DNS Server 1 | Not configured | 10.10.50.21 |
| DNS Server 2 | Not configured | 10.10.51.21 |
| Domain lookup | Failed | Working |

This was an important finding: the problem workstation was not just missing DNS. Its IP configuration also differed from nearby working machines.

## Switch Port and Cable Test

To exclude a physical network issue, the cables were swapped between the problem workstation and a working workstation on the Cisco switch.

Result:

- The working workstation continued to work.
- The problem workstation continued to fail.

Conclusion:

```text
The cable and switch port were not the root cause.
The issue stayed with the workstation or its local network configuration.
```

## Administrative Rights Check

Changing the adapter settings required administrative rights. When opening adapter properties, Windows displayed a UAC prompt and attempted to validate a domain admin account. Because the domain was not reachable, the credential could not be verified.

Local administrators were checked:

```cmd
net localgroup administrators
```

Example anonymized result:

```text
Administrator
localadmin1
localadmin2
```

The built-in local Administrator account was active, but the password was not available during troubleshooting.

## Findings

The investigation showed the following:

- The NIC had link.
- The local gateway responded.
- The cable and Cisco switch port were tested and excluded.
- DNS servers were missing from the problem workstation.
- The domain name could not be resolved.
- The domain controller could not be discovered.
- The problem workstation used a different subnet and gateway than nearby working machines.
- Changing the adapter settings required local administrative access.

## Conclusion

The most likely root cause was incorrect static network configuration on the workstation, including missing DNS servers. The workstation was configured differently from working machines in the same area and could not reach the domain infrastructure.

The issue was not caused by the physical cable or the switch port.

## Proposed Fix

Log in with a local administrator account or use another approved administrative method. Then open:

```text
Control Panel -> Network Connections -> Ethernet -> Properties -> Internet Protocol Version 4 (TCP/IPv4)
```

Review and correct the IPv4 configuration according to the correct network segment.

At minimum, configure the internal DNS servers used by working machines:

```text
Preferred DNS server: 10.10.50.21
Alternate DNS server: 10.10.51.21
```

If the workstation is supposed to be in the same network as nearby working machines, also verify the IP address, subnet mask, and gateway with the network team before applying changes.

After changes, run:

```cmd
ipconfig /flushdns
nslookup corp.local
nltest /dsgetdc:corp.local
```

Expected result:

- The domain name resolves successfully.
- A domain controller is discovered.
- Domain credentials can be validated again.
