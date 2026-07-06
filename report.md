# Troubleshooting Report

## Summary

A Windows workstation in an isolated corporate network segment was unable to access domain services. The workstation had network link and could reach its local gateway, but it could not resolve the corporate domain name or discover a domain controller.

## Work Performed

1. Checked the workstation IP configuration with `ipconfig /all`.
2. Confirmed that the workstation had a static IPv4 address.
3. Verified that the default gateway responded to ping.
4. Checked DNS configuration with `netsh interface ipv4 show dnsservers`.
5. Confirmed that IPv4 DNS servers were not configured.
6. Tested domain resolution with `nslookup` and `ping`.
7. Tested domain controller discovery with `nltest /dsgetdc`.
8. Checked the routing table with `route print`.
9. Checked ARP entries with `arp -a`.
10. Compared the configuration with working workstations in the same area.
11. Swapped network cables/ports with a working workstation to exclude cable and Cisco switch port issues.
12. Checked local administrative group membership.
13. Confirmed that changing adapter settings requires local administrative credentials.

## Key Evidence

Problem workstation:

```text
IPv4 Address: 10.10.40.164
Subnet Mask: 255.255.255.240
Default Gateway: 10.10.40.161
DNS Servers: Not configured
```

Working workstation:

```text
IPv4 Address: 10.10.40.105
Subnet Mask: 255.255.255.192
Default Gateway: 10.10.40.65
DNS Servers: 10.10.50.21, 10.10.51.21
```

Domain check:

```text
nslookup corp.local -> failed
nltest /dsgetdc:corp.local -> ERROR_NO_SUCH_DOMAIN
```

Switch/cable test:

```text
Cable and switch port were swapped with a working workstation.
The issue stayed with the problem workstation.
```

## Conclusion

The physical connection, cable, and switch port were not the root cause. The issue is most likely caused by incorrect static network configuration on the problem workstation, especially missing DNS servers. The workstation also uses a different subnet and gateway from nearby working machines, so the full IPv4 configuration should be reviewed before applying changes.

## Recommended Fix

Use a local administrator account to open the Ethernet adapter IPv4 settings and configure the correct network parameters.

At minimum, configure internal DNS servers:

```text
Preferred DNS: 10.10.50.21
Alternate DNS: 10.10.51.21
```

Also verify with the network team whether the workstation should remain in the `10.10.40.160/28` segment or be configured like nearby working workstations.

After applying changes, verify:

```cmd
ipconfig /flushdns
nslookup corp.local
nltest /dsgetdc:corp.local
```
