# Day 38: Pull Docker Image

## Objective

Nautilus project developers are planning to start testing on a new project. As per their meeting with the DevOps team, they want to test containerized environment application features. As per details shared with DevOps team, we need to accomplish the following task:


a. Pull `busybox:musl` image on `App Server 1` in Stratos DC and re-tag (create new tag) this image as `busybox:media`.

## Solution

Once you're connected to `Application Server 1` via SSH, we'll use `docker pull` to pull :u the requested image

```bash
docker pull busybox:musl
# Output
musl: Pulling from library/busybox
5c3b447848a9: Pull complete 
Digest: sha256:32b5cdad7cce41dfd53d0ae06baebcf8357a147ee7694dc706911c373bc30c37
Status: Downloaded newer image for busybox:musl
docker.io/library/busybox:musl
```

once the image has been downloaded, we'll use `tag` to create the new tag, we're done

```bash
docker tag busybox:musl busybox:media
docker images
# Output
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
busybox      media     654fc8fd836e   3 months ago   1.53MB
busybox      musl      654fc8fd836e   3 months ago   1.53MB
```
