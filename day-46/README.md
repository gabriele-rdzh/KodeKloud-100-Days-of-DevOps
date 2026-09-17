# Day 46: Deploy an App on Docker Containers

## Objective

The Nautilus Application development team recently finished development of one of the apps that they want to deploy on a containerized platform. The Nautilus Application development and DevOps teams met to discuss some of the basic pre-requisites and requirements to complete the deployment. The team wants to test the deployment on one of the app servers before going live and set up a complete containerized stack using a docker compose fie. Below are the details of the task:



  1. On `App Server 1` in `Stratos Datacenter` create a docker compose file `/opt/data/docker-compose.yml` (should be named exactly).


  2. The compose should deploy two services (web and DB), and each service should deploy a container as per details below:


`For web service:`


a. Container name must be `php_blog`.


b. Use image `php` with any `apache` tag.


c. Map `php_blog` container's port `80` with host port `6200`


d. Map `php_blog` container's `/var/www/html` volume with host volume `/var/www/html`.


`For DB service:`


a. Container name must be `mysql_blog`.


b. Use image `mariadb` with any tag (preferably `latest`).


c. Map `mysql_blog` container's port `3306` with host port `3306`


d. Map `mysql_blog` container's `/var/lib/mysql` volume with host volume `/var/lib/mysql`.


e. Set MYSQL_DATABASE=`database_blog` and use any custom user ( except root ) with some complex password for DB connections.


  3. After running docker-compose up you can access the app with curl command `curl <server-ip or hostname>:6200/`


## Solution
Let's create the directory and navigate to it to create the YML file.
```bash
mkdir -p /opt/data
cd /opt/data
```

Here is the YML file, with the volumes and the ports already mapped. Remember that to map them, use the following format: `<host>:<container>`, along with the image tag we need.
```yml
services:
  web:
    container_name: php_blog
    image: php:apache
    ports:
      - "6200:80"
    volumes:
      - /var/www/html:/var/www/html

  DB:
    container_name: mysql_blog
    image: mariadb:latest
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
    environment:
      MYSQL_DATABASE: database_blog
      MYSQL_USER: web_user
      MYSQL_PASSWORD: ComplexPassword123!
      MYSQL_ROOT_PASSWORD: RootComplexPassword123!
```
Use `compose up -d` to set up the service
```bash
docker compose up -d
# Output
[+] up 28/28
 ✔ Image php:apache     Pulled                                                                                             15.3s
 ✔ Image mariadb:latest Pulled                                                                                             11.9s
 ✔ Network data_default Created                                                                                            0.1s
 ✔ Container mysql_blog Created                                                                                            0.2s
 ✔ Container php_blog   Created                                                                                            0.2s
```
and now `curl` to check that everything is working properly
```bash
curl http://localhost:6200/
# Output
<html>
    <head>
        <title>Welcome to xFusionCorp Industries!</title>
    </head>

    <body>
        Welcome to xFusionCorp Industries!    
    </body>
</html>
```
