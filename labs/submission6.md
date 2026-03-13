### Task 1

#### 1.1: Basic Container Operations

```bash
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

---

```bash
$ docker pull ubuntu:latest
latest: Pulling from library/ubuntu
Digest: sha256:d1e2e92c075e5ca139d51a140fff46f84315c0fdce203eab2807c7e495eff4f9
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest

$ docker images ubuntu
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ubuntu       24.04     d1e2e92c075e   4 weeks ago   139MB
ubuntu       latest    d1e2e92c075e   4 weeks ago   139MB
```

---

```bash
$ docker run -it --name ubuntu_container ubuntu:latest
root@589c2c50c199:/# cat /etc/os-release
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

root@589c2c50c199:/# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.7  0.0   4296  3588 pts/0    Ss   15:44   0:00 /bin/bash
root        10  100  0.0   7628  3508 pts/0    R+   15:45   0:00 ps aux
root@589c2c50c199:/# exit
exit
```

#### 1.2: Image Export and Dependency Analysis

```bash
$ docker save -o ubuntu_image.tar ubuntu:latest
$ ls -lh ubuntu_image.tar
-rw-------@ 1 v1adych  staff    28M Mar 13 18:45 ubuntu_image.tar
```

---

```bash
$ docker rmi ubuntu:latest
Error response from daemon: conflict: unable to delete ubuntu:latest (must be forced) - container 5993814e82c0 is using its referenced image d1e2e92c075e
```

---

```bash
$ docker rm ubuntu_container
ubuntu_container

$ docker rmi ubuntu:latest
Untagged: ubuntu:latest
Deleted: sha256:d1e2e92c075e5ca139d51a140fff46f84315c0fdce203eab2807c7e495eff4f9
```

### Observations and Analysis

- `docker images ubuntu` shows image size: `139MB`.
- Exported tar file size: `28M`.
- Tar file is smaller than image size in local listing.
- First `docker rmi ubuntu:latest` failed with conflict, because `ubuntu_container` was still using image `d1e2e92c075e`.
- After `docker rm ubuntu_container`, image removal succeeded (`Untagged` + `Deleted`).
- Image removal fails while at least one existing container references that image.
- Exported tar file includes image metadata + layers needed to load the image later with `docker load`.

### Task 2

#### 2.1: Deploy and Customize Nginx

```bash
$ docker run -d -p 80:80 --name nginx_container nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
a87363d30ab0: Pull complete
d456cad1d0ff: Pull complete
2e1e80a9149a: Pull complete
fbeac1abb084: Pull complete
fca7a914ec95: Pull complete
7e3a4af256ee: Pull complete
Digest: sha256:bc45d248c4e1d1709321de61566eb2b64d4f0e32765239d66573666be7f13349
Status: Downloaded newer image for nginx:latest
9feec4611bb271c57957a57677c8167d69012dbc8b399b9c8859949d3d2088f8

$ curl http://localhost
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy,
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

---

```bash
$ docker cp labs/index.html nginx_container:/usr/share/nginx/html/
Successfully copied 2.05kB to nginx_container:/usr/share/nginx/html/

$ curl http://localhost
<html>
<head>
<title>The best</title>
</head>
<body>
<h1>website</h1>
</body>
</html>
```

#### 2.2: Create and Test Custom Image

```bash
$ docker commit nginx_container my_website:latest
sha256:1d36e036430fa8fec08ed6cc56b9f4130d3b468c8b42db1cbc817056c1620380

$ docker images my_website
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
my_website   latest    1d36e036430f   6 seconds ago   255MB
```

---

```bash
$ docker rm -f nginx_container
nginx_container

$ docker run -d -p 80:80 --name my_website_container my_website:latest
cd9226a10e633eff801810c3dd918535ae9e7cda4721ad803db082f69217413e

$ curl http://localhost
<html>
<head>
<title>The best</title>
</head>
<body>
<h1>website</h1>
</body>
</html>
```

---

```bash
$ docker diff my_website_container
C /etc
C /etc/nginx
C /etc/nginx/conf.d
C /etc/nginx/conf.d/default.conf
C /run
C /run/nginx.pid
```

### Observations and Analysis

- `curl http://localhost` first returned default Nginx welcome page.
- After copy, `curl http://localhost` returned custom HTML (`The best` / `website`).
- `docker diff my_website_container` output has only `C` markers, so some existing files/dirs were changed, no added (`A`) or deleted (`D`) entries in this run.
- `docker commit` is fast for quick experiments and preserves current container state directly.
- Dockerfile is better for reproducibility, reviewability, and sharing build steps in git.
- Main downside of `docker commit`: harder to track exactly what changed compared to Dockerfile instructions.

### Task 3

#### 3.1: Create Custom Network

```bash
$ docker network create lab_network
cb403d60b0a1b1d66733669fe32ef06c2838d7ec2e85fbbbe39d79309d89391c

$ docker network ls
NETWORK ID     NAME          DRIVER    SCOPE
7e78287040be   bridge        bridge    local
922807aeaf14   host          host      local
cb403d60b0a1   lab_network   bridge    local
746d67d55341   none          null      local
```

---

```bash
$ docker run -dit --network lab_network --name container1 alpine ash
86b2d2fa99ace99712d7fd8f0396795d8ebdd03a3c5f61d62e07f6d4b98650ae

$ docker run -dit --network lab_network --name container2 alpine ash
6be4f859c259c617a74296fbc741505926138b8088dffeaf969ea508dc8dd287
```

#### 3.2: Test Connectivity and DNS

```bash
$ docker exec container1 ping -c 3 container2
PING container2 (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.110 ms
64 bytes from 172.18.0.3: seq=1 ttl=64 time=0.141 ms
64 bytes from 172.18.0.3: seq=2 ttl=64 time=0.187 ms

--- container2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.110/0.146/0.187 ms
```

---

```bash
$ docker network inspect lab_network
[
    {
        "Name": "lab_network",
        "Id": "cb403d60b0a1b1d66733669fe32ef06c2838d7ec2e85fbbbe39d79309d89391c",
        "Created": "2026-03-13T16:02:50.821276795Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv4": true,
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "6be4f859c259c617a74296fbc741505926138b8088dffeaf969ea508dc8dd287": {
                "Name": "container2",
                "EndpointID": "8048cd18a8e15d192b96046fd349a12c60163d4fed2c4f912cb0002a6356deab",
                "MacAddress": "6a:7c:b4:a9:aa:56",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            },
            "86b2d2fa99ace99712d7fd8f0396795d8ebdd03a3c5f61d62e07f6d4b98650ae": {
                "Name": "container1",
                "EndpointID": "e730da44c7d0c444ac89d98bac8068e1e3c7406ee309c3520255425550784283",
                "MacAddress": "4a:e9:37:f4:54:21",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.enable_ipv4": "true",
            "com.docker.network.enable_ipv6": "false"
        },
        "Labels": {}
    }
]
```

---

```bash
$ docker exec container1 nslookup container2
Server:         127.0.0.11
Address:        127.0.0.11:53

Non-authoritative answer:

Non-authoritative answer:
Name:   container2
Address: 172.18.0.3
```

### Observations and Analysis

- `ping` from `container1` to `container2` succeeded with `0% packet loss`.
- `docker network inspect lab_network` shows both containers in same subnet: `container1 = 172.18.0.2`, `container2 = 172.18.0.3`.
- `nslookup container2` from `container1` resolved name to `172.18.0.3` using Docker DNS `127.0.0.11`.
- Docker internal DNS lets containers communicate by container name, no manual `/etc/hosts` needed.
- User-defined bridge network is more convenient than default bridge: automatic DNS by name, cleaner isolation for project containers, and easier service grouping.
