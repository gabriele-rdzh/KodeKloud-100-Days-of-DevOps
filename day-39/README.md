# Day 39: Create a Docker Image From Container

## Objective

One of the Nautilus developer was working to test new changes on a container. He wants to keep a backup of his changes to the container. A new request has been raised for the DevOps team to create a new image from this container. Below are more details about it:


a. Create an image `news:xfusion` on `Application Server 2` from a container `ubuntu_latest` that is running on same server.

## Solution

In `Application Server 2` we're going to use `docker commit` to create a new image from a container. It has additional options, such as `author`, `message` and `change`, but we won't need them for now.
We'll only need the container ID
```bash
docker ps
# Output
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
3413a6b4ef23   ubuntu    "/bin/bash"   4 minutes ago   Up 4 minutes             ubuntu_latest
```

Now that we have the container ID, let's use `commit` as follows:
```bash
docker commit 3413a6b4ef23 news:xfusion
# Output
sha256:f9d135925264d0a408c81a4cbb19ffe32700b4c6f562670cb8103c1427d5e565
```

Finally, we can check the images and see that out image was created correctly
```bash
docker images
# Output
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
news         xfusion   f9d135925264   6 seconds ago   142MB
ubuntu       latest    af52039db3f8   2 weeks ago     100MB
```
