# Lessons Learned

## 1. "No Internet" is not always the real issue

In an isolated corporate network, Windows may show "No Internet" even when internal network access is expected to work. The real problem should be validated with internal resources, not public websites.

## 2. DNS is critical for domain connectivity

If DNS is missing or wrong, Active Directory domain discovery will fail even when the gateway is reachable.

## 3. Gateway reachability does not prove full network access

The problem workstation could reach its local gateway, but could not resolve the domain or reach domain services.

## 4. Compare with a known-good workstation

The biggest clue came from comparing the problem workstation with nearby working machines.

## 5. Swap tests are useful

Swapping cables and switch ports helped exclude physical layer and access port issues.

## 6. Administrative access matters

The fix required adapter configuration changes, but the current user had no local administrative rights. Since the domain was unavailable, domain admin credentials could not be validated through UAC.
