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
$ docker cp index.html nginx_container:/usr/share/nginx/html/
lstat /Users/v1adych/inno/devops/DevOps-Intro/index.html: no such file or directory

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
