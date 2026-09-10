# Day 43: Docker Ports Mapping

## Objective

The Nautilus DevOps team is planning to host an application on a nginx-based container. There are number of tickets already been created for similar tasks. One of the tickets has been assigned to set up a nginx container on `Application Server 2` in `Stratos Datacenter`. Please perform the task as per details mentioned below:


a. Pull `nginx:alpine` docker image on `Application Server 2`.


b. Create a container named `demo` using the image you pulled.


c. Map host port `6200` to container port `80`. Please keep the container in running state.

## Solution

First, let's download the `nginx:alpine` image
```bash
docker pull nginx:alpine
# Output
alpine: Pulling from library/nginx
55afa1ecc21d: Pull complete 
850bf2dcecff: Pull complete 
af7dd138f459: Pull complete 
58c524ea09ce: Pull complete 
bc98d7675616: Pull complete 
51900e10fb9c: Pull complete 
8f924cf5086c: Pull complete 
6636b9fc203c: Pull complete 
Digest: sha256:72ba65eb42c10344912a84ff42408db7d34f2feb642204570ab8fc5ffd29f1d3
Status: Downloaded newer image for nginx:alpine
docker.io/library/nginx:alpine
```

Now, to keep the container running in the backgroud, we0ll use `-d` and to map the ports, we'll use `-p`, with the ports listed as the host port first, followed by the containerport.
```bash
docker run -d --name demo -p 6200:80 nginx:alpine
# Output
67a4df2d0b038bf26de12c90b59d6ac3cc877796d026bcb1ff9839603efa8126
```

Finally, we can use `ps` to verify the ports are mapped, that the container was created using the `nginx:alpine` image, and that its name is demo.
```bash
docker ps
# Output
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                   NAMES
67a4df2d0b03   nginx:alpine   "/docker-entrypoint.…"   24 seconds ago   Up 22 seconds   0.0.0.0:6200->80/tcp, :::6200->80/tcp   demo
```

