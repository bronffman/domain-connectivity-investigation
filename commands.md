# Commands Used During Troubleshooting

This file contains the commands used during the investigation and what each command was used for.

## Check IP Configuration

```cmd
ipconfig /all
```

Used to check:

- IPv4 address
- Subnet mask
- Default gateway
- DNS servers
- DNS suffix
- DHCP status

## Check Configured DNS Servers

```cmd
netsh interface ipv4 show dnsservers
```

Used to confirm whether IPv4 DNS servers were configured on the Ethernet adapter.

## Test Gateway Reachability

```cmd
ping 10.10.40.161
tracert 10.10.40.161
```

Used to verify that the workstation could reach its default gateway.

## Test DNS Servers

```cmd
ping 10.10.50.21
ping 10.10.51.21
tracert 10.10.50.21
```

Used to check whether internal DNS servers were reachable from the problem workstation.

## Test Domain Name Resolution

```cmd
nslookup corp.local
ping corp.local
```

Used to check whether the corporate domain name could be resolved.

## Discover Domain Controller

```cmd
nltest /dsgetdc:corp.local
```

Used to verify whether the workstation could locate a domain controller.

## Check Routes

```cmd
route print
```

Used to inspect the IPv4 routing table.

## Check ARP Table

```cmd
arp -a
```

Used to confirm that local gateway addresses were visible on Layer 2.

## Check Current User

```cmd
whoami
net user %username%
```

Used to confirm whether the current user had local administrative privileges.

## Check Local Administrators

```cmd
net localgroup administrators
net user administrator
```

Used to identify local administrator accounts and check whether the built-in Administrator account was active.
