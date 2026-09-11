# Day 44: Write a Docker Compose File

## Objective

The Nautilus application development team shared static website content that needs to be hosted on the `httpd` web server using a containerised platform. The team has shared details with the DevOps team, and we need to set up an environment according to those guidelines. Below are the details:



a. On `App Server 2` in `Stratos DC` create a container named `httpd` using a docker compose file `/opt/docker/docker-compose.yml` (please use the exact name for file).


b. Use `httpd`(preferably `latest` tag) image for container and make sure container is named as `httpd`; you can use any name for service.


c. Map `80` number port of container with port `5004` of docker host.


d. Map container's `/usr/local/apache2/htdocs` volume with `/opt/dba` volume of docker host which is already there. (please do not modify any data within these locations).

## Solution

First, let's create the directory and the YML file we'll need.
```bash
sudo mkdir -p /opt/docker
sudo vi /opt/docker/docker-compose.yml
```

This is how our YML file should look; we can see that the ports and volumes are already mapped.

`Note`: the line where we used to write `version: 3.x` is no longer necessary; if its include, docker will ignore it
```yml
services:
  apache-service:
    image: httpd:latest
    container_name: httpd
    ports:
      - "5004:80"
    volumes:
      - /opt/dba:/usr/local/apache2/htdocs
```

Now let's move to the docker directory—the one we just created—to start our service
```bash
cd /opt/docker/
docker compose up -d
# Output
[+] up 9/9
 ✔ Image httpd:latest     Pulled                                                                                            3.7s
 ✔ Network docker_default Created                                                                                           0.1s
 ✔ Container httpd        Created                                                                                           0.2s
```

Once our service has been created, we can chack it with `docker ps`
```bash
docker ps
# Output
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS          PORTS                                   NAMES
2849cc175406   httpd:latest   "httpd-foreground"   12 seconds ago   Up 11 seconds   0.0.0.0:5004->80/tcp, :::5004->80/tcp   httpd
```



