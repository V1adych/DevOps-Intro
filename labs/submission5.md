# Lab 5 Submission

## Task 1 - VirtualBox Installation

- Host OS: `macOS Tahoe 26.2`
- VirtualBox version: `7.2.6 r172322 (Qt6.8.0 on cocoa)`
- Installation issues: none

## Task 2 - Ubuntu Environment and System Analysis


### Environment Configuration Used


  - CPUs: `8`
  - Memory: `8GiB`
  - Disk: `512GiB`


### CPU Details

- Tool(s): `lscpu`
- Command(s): `lscpu`
- Output: `CPU(s): 8`

### Memory Information

- Tool(s): `free`, `/proc/meminfo`
- Command(s): `free -h`, `cat /proc/meminfo | grep -E 'MemTotal|MemAvailable'`
- Output: `Mem: 7.7Gi `

**command:**
```bash
$ free -h
```
**output:**
```bash
               total        used        free      shared  buff/cache   available
Mem:           7.7Gi       2.2Gi       4.4Gi       752Ki       1.2Gi       5.4Gi
Swap:          1.0Gi          0B       1.0Gi
```

**command:**
```bash
$ cat /proc/meminfo | grep -E 'MemTotal|MemAvailable'
```
**output:**
```bash
MemTotal:        8025700 kB
MemAvailable:    5671376 kB
```

### Network Configuration

- Tool(s): `ip`
- Command(s): `ip a`
- Output: `lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000`

**command:**
```bash
$ ip a
```
**output:**
```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
...
```

### Storage Information

- Tool(s): `df`, `lsblk`
- Command(s): `df -h`, `lsblk`
- Output: `overlay total: 503G avail: 61G used: 417G 13% mounted on /`

**command:**
```bash
$ df -h
```
**output:**
```bash
Filesystem      Size  Used Avail Use% Mounted on
overlay         503G   61G  417G  13% /
tmpfs            64M     0   64M   0% /dev
shm              64M     0   64M   0% /dev/shm
/dev/vda1       503G   61G  417G  13% /etc/hosts
tmpfs           3.9G     0  3.9G   0% /proc/scsi
tmpfs           3.9G     0  3.9G   0% /sys/firmware
```

**command:**
```bash
$ lsblk
```
**output:**
```bash
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
vda    254:0    0   512G  0 disk
`-vda1 254:1    0   512G  0 part /etc/hosts
                                 /etc/hostname
                                 /etc/resolv.conf
vdb    254:16   0 572.6M  1 disk
```

### Operating System Details

- Tool(s): `cat`, `uname`
- Command(s): `cat /etc/os-release`, `uname -a`
- Output: `Ubuntu 24.04.4 LTS`

**command:**
```bash
$ cat /etc/os-release
```
**output:**
```bash
PRETTY_NAME="Ubuntu 24.04.4 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.4 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=noble
LOGO=ubuntu-logo
```

