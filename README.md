# Failed SSH Authentication Investigation

## Overview

A small, controlled investigation of failed SSH authentication events on my Ubuntu virtual machine.

I generated three failed login attempts against the SSH service running on the same VM, then reviewed the resulting system logs.

This is a learning exercise, not an investigation of an external attack.

## Objectives

- Find failed SSH authentication events in system logs
- Identify the attempted username, source IP address, timestamps, and source ports
- Summarise the events in a short timeline
- Distinguish observed facts from assumptions

## Lab Environment

- Ubuntu running in VMware
- OpenSSH server
- `journalctl` and `/var/log/auth.log`
- Test connection to `127.0.0.1` from the same VM

## Evidence

Selected, sanitised log entries from the controlled test are available in [`evidence/failed-ssh-events.log`](evidence/failed-ssh-events.log).

The evidence file contains only relevant SSH entries. The full system log is not included.

## Investigation Method

1. Confirmed that the SSH service was running.
2. Made three intentional, failed SSH login attempts using the test username `fakeuser`.
3. Connected to `127.0.0.1`, the loopback address of the same VM.
4. Reviewed SSH events with `journalctl` and `/var/log/auth.log`.
5. Recorded the timestamp, attempted username, source address, and source port for each attempt.

Example command used to review recent SSH events:

```bash
sudo journalctl -u ssh --since "5 minutes ago" --no-pager
```

## Findings

Three failed SSH authentication attempts were observed.

| Attempt | Time of failed-password event | Source IP | Source port | Username |
|---|---|---|---|---|
| 1 | 13:14:55 | 127.0.0.1 | 33232 | fakeuser |
| 2 | 13:15:42 | 127.0.0.1 | 38868 | fakeuser |
| 3 | 13:15:57 | 127.0.0.1 | 56456 | fakeuser |

The logs report `fakeuser` as an invalid user. The source address `127.0.0.1` is the loopback address, so these test connections originated from the same VM. The listed source ports are temporary client-side ports, not the SSH service port.

## Conclusion

The logs confirm three failed SSH authentication attempts for the invalid username `fakeuser`, all originating from the local VM.

Because these events were intentionally generated as part of a lab exercise, they are not evidence of an external attacker or a real-world attack. The log entries alone also do not establish malicious intent.

## Limitations

- The exercise used only local loopback connections.
- It did not analyse external hosts or production systems.
- The test does not evaluate detection coverage or prove that a real attack occurred.
- The event times are shown as recorded in the VM; the date and timezone should be interpreted in the context of that system.

## Skills Practised

- Reviewing Linux authentication logs
- Using `journalctl`
- Recognising failed SSH authentication events
- Extracting timestamps, usernames, IP addresses, and ports
- Building a basic event timeline
- Writing evidence-based conclusions

## Safety and Privacy

All attempts were generated against my own Ubuntu VM. The repository contains selected log entries only; it does not include the full system log or the packet capture.

## Related Projects

- [Cybersecurity Portfolio](https://github.com/kamilbigaj/cybersecurity-portfolio)
- [Network Traffic Investigation](https://github.com/kamilbigaj/network-traffic-investigation)
