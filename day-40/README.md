# Day 40: Docker EXEC Operations

## Objective

One of the Nautilus DevOps team members was working to configure services on a `kkloud` container that is running on `App Server 3` in  `Stratos Datacenter`. Due to some personal work he is on PTO for the rest of the week, but we need to finish his pending work ASAP. Please complete the remaining work as per details given below:


a. Install `apache2` in `kkloud` container using `apt` that is running on `App Server 3` in `Stratos Datacenter`.


b. Configure Apache to listen on port `3002` instead of default `http` port. Do not bind it to listen on specific IP or hostname only, i.e it should listen on localhost, 127.0.0.1, container ip, etc.


c. Make sure Apache service is up and running inside the container. Keep the container in running state at the end.

## Solution

To install and configure Apache2 inside a container, we can use `exec -it`. This allows us to enter the container and run a terminal. We can see the change in the user
```bash
[banner@stapp03 ~]$ docker exec -it kkloud /bin/bash
root@479d56a327ab:/# apt update
root@479d56a327ab:/# apt install -y apache2
```

Since neither vi nor nano is installed on the container, we'll use `sed` to change the ports
```bash
root@479d56a327ab:/# sed -i 's/Listen 80/Listen 3002/g' /etc/apache2/ports.conf
root@479d56a327ab:/# sed -i 's/<VirtualHost \*:80>/<VirtualHost \*:3002>/g' /etc/apache2/sites-enabled/000-default.conf
```

Now we can start the service and use curl to verify that everything has been configured correctly
```bash
root@479d56a327ab:/# service apache2 start
root@479d56a327ab:/# curl http:///localhost:3002
root@479d56a327ab:/# exit
```

