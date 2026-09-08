# Day 41: Write a Docker File

## Objective

As per recent requirements shared by the Nautilus application development team, they need custom images created for one of their projects. Several of the initial testing requirements are already been shared with DevOps team. Therefore, create a docker file `/opt/docker/Dockerfile` (please keep `D`
capital of Dockerfile) on `App server 1` in `Stratos DC` and configure to build an image with the following requirements:



a. Use `ubuntu:24.04` as the base image.


b. Install `apache2` and configure it to work on `6400` port. (do not update any other Apache configuration settings like document root etc).

## Solution

Once on `App server 1`, we'll create the directory and navigate to it to create the Dockerfile.
```bash
sudo mkdir -p /opt/docker
cd /opt/docker
```

Here is the Dockerfile
```Dockerfile
# We use Ubuntu as the base image
FROM ubuntu:24.04

# this is to prevent interactive interruptions during installation
ENV DEBIAN_FRONTEND=noninteractive

# Heri we install apache2
RUN apt update && \
    apt install -y apache2

# And here we configure it using `sed` to change the ports
RUN sed -i 's/Listen 80/Listen 6400/g' /etc/apache2/ports.conf && \
    sed -i 's/<VirtualHost \*:80>/<VirtualHost \*:6400>/g' /etc/apache2/sites-available/000-default.conf

# We expose port 6400
EXPOSE 6400

# and this is to run Apache in the foreground
CMD ["apache2ctl", "-D", "FOREGROUND"]
```
