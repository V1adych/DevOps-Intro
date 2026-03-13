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
- Image layer count: I did not check it separately in this step.
- First `docker rmi ubuntu:latest` failed with conflict, because `ubuntu_container` was still using image `d1e2e92c075e`.
- After `docker rm ubuntu_container`, image removal succeeded (`Untagged` + `Deleted`).
- Image removal fails while at least one existing container references that image.
- I think exported tar file includes image metadata + layers needed to load the image later with `docker load`.
