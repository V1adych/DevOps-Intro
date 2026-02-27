### Task 1

#### 1.1: Boot Performance Analysis
Unfortunatelly, my system does not have systemd installed, so I cannot use the `systemd-analyze` command.

```bash
$ uptime
21:40  up 4 days, 11:51, 4 users, load averages: 1.94 2.04 1.91

$ w
21:40  up 4 days, 11:51, 4 users, load averages: 1.94 2.04 1.91
USER       TTY      FROM    LOGIN@  IDLE WHAT
v1adych    console  -      Mon09   4days -
v1adych    s007     -      Mon09   4days tmux new -s klbk
v1adych    s012     -      Mon10   24:03 -fish
v1adych    s015     -      Mon13   4days -fish
```

#### 1.2: Process Forensics
```bash
$ ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -n 6
  PID  PPID CMD                         %MEM %CPU
    1     0 /bin/bash                    0.0  0.0
    9     1 ps -eo pid,ppid,cmd,%mem,%c  0.0  0.0
    10     1 head -n 6                    0.0  0.0
```
---
```bash
$ ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head -n 6
  PID  PPID CMD                         %MEM %CPU
    1     0 /bin/bash                    0.0  0.0
    11     1 ps -eo pid,ppid,cmd,%mem,%c  0.0  0.0
    12     1 head -n 6                    0.0  0.0
```

#### 1.3: Service Dependencies
For the same reason as in `1.1`, I cannot use the `systemctl` command.

#### 1.4: User Sessions
The system shows multiple active sessions for the user `v1adych`, primarily using `fish` shell and `tmux`.

```bash
$ who -a
                 system boot  Feb 23 09:49 
v1adych          console      Feb 23 09:50 
v1adych          ttys000      Feb 23 09:51      term=0 exit=0
v1adych          ttys001      Feb 23 09:51      term=0 exit=0
v1adych          ttys007      Feb 23 09:53 
v1adych          ttys010      Feb 23 09:53      term=0 exit=0
v1adych          ttys012      Feb 23 10:42 
v1adych          ttys015      Feb 23 13:27 
   .       run-level 3
```
---
```bash
$ last -n 5
v1adych    ttys015                         Mon Feb 23 13:27   still logged in
v1adych    ttys012                         Mon Feb 23 10:42   still logged in
v1adych    ttys007                         Mon Feb 23 09:53   still logged in
v1adych    ttys010                         Mon Feb 23 09:53 - 09:53  (00:00)
v1adych    ttys007                         Mon Feb 23 09:52 - 09:52  (00:00)
```

#### 1.5: Memory Analysis
*launched from docker*:
```bash
$ free -h
               total        used        free      shared  buff/cache   available
Mem:           7.7Gi       2.3Gi       143Mi       628Ki       5.4Gi       5.3Gi
Swap:          1.0Gi          0B       1.0Gi
```
---
```bash
$ cat /proc/meminfo | grep -e MemTotal -e SwapTotal -e MemAvailable
MemTotal:        8025700 kB
MemAvailable:    5574028 kB
SwapTotal:       1048572 kB
```

### Observations and Analysis
- **Key Observations:** 
  - The system has been stable for over 4 days.
  - Load averages are around 2.0, which is normal for a multi-core system but indicates some active processing.
  - Memory usage shows a large amount of "buff/cache" (5.4Gi)
  - The user has several persistent sessions, likely due to the use of `tmux`.
- **Top memory-consuming process:** Based on the `ps` output, `/bin/bash` is listed, but all captured processes show 0.0% memory usage. In a real scenario with more processes, this would likely be a web browser or a developer tool like VS Code.
- **Resource utilization patterns:** *Might be inaccurate due to the fact that this command was executed from docker* (high available memory despite low "free" memory). Swap is completely unused, indicating that physical RAM is sufficient for the current workload.

### Task 2

#### 2.1: Network Path Tracing

```bash
$ traceroute github.com
traceroute to github.com (140.82.121.3), 64 hops max, 40 byte packets
 1  10.8.1.0 (10.8.1.0)  55.424 ms  50.387 ms  51.134 ms
 2  172.29.172.1 (172.29.172.1)  51.737 ms  54.069 ms  53.492 ms
 3  5.253.63.3 (5.253.63.3)  54.751 ms  71.375 ms  56.498 ms
 4  5.39.223.251 (5.39.223.251)  47.186 ms  45.318 ms  47.457 ms
 5  as5405.frys-ix.net (185.1.160.9)  47.145 ms  48.474 ms  48.521 ms
 6  * * *
 7  * * *
 8  * * *
 ...
```
---
```bash
$ dig github.com

; <<>> DiG 9.10.6 <<>> github.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 39662
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;github.com.                    IN      A

;; ANSWER SECTION:
github.com.             12      IN      A       140.82.121.3

;; Query time: 56 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Fri Feb 27 22:06:48 MSK 2026
;; MSG SIZE  rcvd: 55
```

#### 2.2: Packet Capture
*ran from docker*:
```bash
$ timeout 10 tcpdump -c 5 -i any 'port 53' -nn
tcpdump: data link type LINUX_SLL2
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on any, link-type LINUX_SLL2 (Linux cooked v2), snapshot length 262144 bytes

0 packets captured
0 packets received by filter
0 packets dropped by kernel
```

#### 2.3: Reverse DNS
```bash
$ dig -x 8.8.4.4

; <<>> DiG 9.10.6 <<>> -x 8.8.4.4
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 18558
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;4.4.8.8.in-addr.arpa.          IN      PTR

;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.   71431   IN      PTR     dns.google.

;; Query time: 50 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Fri Feb 27 22:10:29 MSK 2026
;; MSG SIZE  rcvd: 73
```
---
```bash
$ dig -x 1.1.2.2

; <<>> DiG 9.10.6 <<>> -x 1.1.2.2
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 16006
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;2.2.1.1.in-addr.arpa.          IN      PTR

;; AUTHORITY SECTION:
1.in-addr.arpa.         3600    IN      SOA     ns.apnic.net. read-txt-record-of-zone-first-dns-admin.apnic.net. 23597 7200 1800 604800 3600

;; Query time: 58 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Fri Feb 27 22:10:55 MSK 2026
;; MSG SIZE  rcvd: 137
```

### Observations and Analysis

**Insights on network paths discovered:**
The first two hops are private network addresses, then traffic goes through public IPs to an internet exchange (`as5405.frys-ix.net`) at hop 5. Hops 6+ return `* * *` — I think these routers are configured to not respond to traceroute probes.

**Analysis of DNS query/response patterns:**
`dig github.com` returned `NOERROR` with a single `A` record. The TTL is 12 seconds, which is quite low — I think GitHub may be using short TTLs for load balancing. The resolver used was `1.1.1.1` (Cloudflare).

**Comparison of reverse lookup results:**
| IP | Status | PTR Record |
|----|--------|------------|
| `8.8.4.4` | `NOERROR` | `dns.google.` |
| `1.1.2.2` | `NXDOMAIN` | None |

`8.8.4.4` has a PTR record pointing to `dns.google.`. `1.1.2.2` has no PTR record — the authority section points to `ns.apnic.net`, so the block is managed by APNIC, but this specific address has no reverse entry.

**Example DNS query from packet capture:**
The `tcpdump` in Docker captured 0 packets due to no DNS activity in the container during that window. Reconstructed from `dig` output:

```
22:06:48.000000 IP 10.8.1.XXX.XXXXX > 1.1.1.1.53: 39662+ A? github.com. (28)
22:06:48.056000 IP 1.1.1.1.53 > 10.8.1.XXX.XXXXX: 39662 1/0/1 A 140.82.121.XXX (55)
```