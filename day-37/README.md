# Day 37: Copy File to Docker Container

## Objective

The Nautilus DevOps team possesses confidential data on `App Server 1` in the `Stratos Datacenter`. A container named `ubuntu_latest` is running on the same server.



Copy an encrypted file `/tmp/nautilus.txt.gpg` from the docker host to the `ubuntu_latest` container located at `/home/`. Ensure the file is not modified during this operation.

## Solution

To copy a fine into a container, we'll use ` docker cp` as follows

```bash
docker cp [file path] [container]:[destination path]
```
Now we'll make the necessary changes, and that's it
```bash
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/home/
# Output
Successfully copied 2.05kB to ubuntu_latest:/home/
```
We can use `exec` to verify that the file was copied correctly
```bash
docker exec -it ubuntu_latest ls -l /home/
# Output
total 8
-rw-r--r-- 1 root   root    105 Sep  2 05:46 nautilus.txt.gpg
drwxr-x--- 2 ubuntu ubuntu 4096 Aug 17 09:02 ubuntu
```
