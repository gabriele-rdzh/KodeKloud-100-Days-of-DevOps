# Day 36: Deploy Nginx Container on Application Server

## Objective

The Nautilus DevOps team is conducting application deployment tests on selected application servers. They require a nginx container deployment on `Application Server 1`. Complete the task with the following instructions:


On `Application Server 1` create a container named `nginx_1` using the `nginx` image with the `alpine` tag. Ensure container is in a `running` state.

## Solution

Once you're connected to `Application Server 1` via SSH, we'll use `docker run` to create the container

```bash
docker run -d --name nginx_1 nginx:alpine
# Output 
Unable to find image 'nginx:alpine' locally
alpine: Pulling from library/nginx
55afa1ecc21d: Pull complete 
d94291c26261: Pull complete 
0ea935727878: Pull complete 
1ed8e39a7434: Pull complete 
0d62d88506ba: Pull complete 
8cdfe4f23778: Pull complete 
da4dea1b00af: Pull complete 
fb597529c916: Pull complete 
Digest: sha256:db35bfc6b2951e7f8a72db5db120288c127ffaeeb4a6d4b95a26fead017d5913
Status: Downloaded newer image for nginx:alpine
e398eb49062c49d9e3c9179977ef08fba235027ed7d4a289466c2010d25e0623
```
Once the container has been created, we'll verify that it's running state

```bash
docker ps
# Output
CONTAINER ID   IMAGE          COMMAND                  CREATED              STATUS              PORTS     NAMES
e398eb49062c   nginx:alpine   "/docker-entrypoint.…"   About a minute ago   Up About a minute   80/tcp    nginx_1
```
