# Incident Report 001: SSH Brute Force Attempt

## Summary
Simulated SSH brute force attack from Kali Linux against the Ubuntu Server in my home lab. Wazuh detected the activity and generated a brute force alert.

## Environment
| Role | Host | IP |
|---|---|---|
| Attacker | Kali Linux | 192.168.255.129 |
| Target | Ubuntu Server | 192.168.255.133 |
| Detection | Wazuh 4.14 | 192.168.255.133 |

## Timeline
| Time | Event |
|---|---|
| Oct 6, 2026 @ 21:09:55.356 | First failed login attempt |
| Oct 6, 2026 @ 21:14:29.431 | Brute force alert triggered |
| Oct 6, 2026 @ 21:14:27.429 | Last failed login attempt |

## Attack Simulation
Multiple failed SSH login attempts using a non-existent user:
```bash
ssh usuarioinventado@192.168.255.133
```

## Detection
- **Rule description:** syslog: User missed the password more than one time
- **Rule ID:** 2502
- **Severity level:** 10
- **Agent:** svr-lab
- **Source IP:** 192.168.255.129
- **MITRE ATT&CK:** T1110 (BRUTE FORCE)

![Wazuh alert](../screenshots/brute-force-alert.png)

## Analysis
Wazuh correlated several failed authentication events coming from the same source IP (192.168.255.129) in a short time window and raised a level 10 alert. This pattern is consistent with a password guessing attempt (MITRE T1110). In this case the source was my own Kali VM, so it is a controlled simulation.

## Response / Recommendations
- Block the source IP with a firewall rule
- Disable password authentication for SSH and use key-based authentication
- Limit login attempts (e.g., fail2ban)
- Monitor for successful logins from the same source after the failures

## Lessons Learned
- A single failed login generates a low-severity event; the high-severity alert appears only after repeated failures, which is how Wazuh separates noise from suspicious behavior.
- Wazuh analyzes logs from the endpoint; it shows *what happened on the host*, but not network traffic like port scans.
