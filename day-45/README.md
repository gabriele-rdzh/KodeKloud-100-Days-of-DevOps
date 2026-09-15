# Day 45: Resolve Dockerfile Issues

## Objective

The Nautilus DevOps team is working to create new images per requirements shared by the development team. One of the team members is working to create a `Dockerfile` on `App Server 1` in `Stratos DC`. While working on it she ran into issues in which the docker build is failing and displaying errors. Look into the issue and fix it to build an image as per details mentioned below:


a. The `Dockerfile` is placed on `App Server 1` under `/opt/docker directory`.


b. Fix the issues with this file and make sure it is able to build the image.


c. Do not change base image, any other valid configuration within Dockerfile, or any of the data been used — for example, index.html.

## Solution

To solve this problem, let's first take a look at the `Dockerfile`
```Dockerfile
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf.d/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf.d/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf.d/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf.d/httpd.conf

COPY certs/server.crt /usr/local/apache2/conf/server.crt

COPY certs/server.key /usr/local/apache2/conf/server.key

COPY html/index.html /usr/local/apache2/htdocs/
```

Based on how we used `sed -i` earlier, we might think the error is there, but in reality, the error is in the path. we can see that it's written as `./conf.d/httpd.conf` instead of `./conf/httpd.conf`.
```Dockerfile
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

COPY certs/server.crt /usr/local/apache2/conf/server.crt

COPY certs/server.key /usr/local/apache2/conf/server.key

COPY html/index.html /usr/local/apache2/htdocs/
```

Once we've corrected the path, we'll create the image and the container to make sure there are no errors.
```bash
docker build .
[+] Building 9.0s (13/13) FINISHED                                                                               docker:default
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 557B                                                                                       0.0s
 => [internal] load metadata for docker.io/library/httpd:2.4.43                                                            1.2s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [1/8] FROM docker.io/library/httpd:2.4.43@sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c27b4d685f0321e65c5d4fd49357      3.1s
 => => resolve docker.io/library/httpd:2.4.43@sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c27b4d685f0321e65c5d4fd49357      0.0s
 => => sha256:3d3fecf6569b94e406086a2b68a7c8930254490b45c0de4911f497ea9cf0876c 146B / 146B                                 0.3s
 => => sha256:b5fc3125d9129e4cdd43f496195cc8f39d43e9bad171044ecb5b8f82b2f6e30d 10.37MB / 10.37MB                           0.6s
 => => sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c27b4d685f0321e65c5d4fd49357 1.86kB / 1.86kB                             0.0s
 => => sha256:53729354a74c9c146aa8726a8906e833755066ada1a478782f4dfb2ea6994b5d 1.37kB / 1.37kB                             0.0s
 => => sha256:f1455599cc2e008a4555f14451e590f071371d371a3b87790651a367357d252c 7.35kB / 7.35kB                             0.0s
 => => sha256:bf59529304463f62efa7179fa1a32718a611528cc4ce9f30c0d1bbc6724ec3fb 27.09MB / 27.09MB                           0.5s
 => => sha256:3c61041685c0f65e0b375bae6ae6bdeab9b6c20960dbef5e30201db18a4e6d4a 24.47MB / 24.47MB                           0.9s
 => => extracting sha256:bf59529304463f62efa7179fa1a32718a611528cc4ce9f30c0d1bbc6724ec3fb                                  1.2s
 => => sha256:34b7e9053f76ca3c9dc574c5034679769256a596008efbfbff1d1b1546600841 298B / 298B                                 0.7s
 => => extracting sha256:3d3fecf6569b94e406086a2b68a7c8930254490b45c0de4911f497ea9cf0876c                                  0.0s
 => => extracting sha256:b5fc3125d9129e4cdd43f496195cc8f39d43e9bad171044ecb5b8f82b2f6e30d                                  0.5s
 => => extracting sha256:3c61041685c0f65e0b375bae6ae6bdeab9b6c20960dbef5e30201db18a4e6d4a                                  0.6s
 => => extracting sha256:34b7e9053f76ca3c9dc574c5034679769256a596008efbfbff1d1b1546600841                                  0.0s
 => [internal] load build context                                                                                          0.0s
 => => transferring context: 3.19kB                                                                                        0.0s
 => [2/8] RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf                                        0.3s
 => [3/8] RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf                                 0.4s
 => [4/8] RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf             0.6s
 => [5/8] RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf                                       0.5s
 => [6/8] COPY certs/server.crt /usr/local/apache2/conf/server.crt                                                         0.2s
 => [7/8] COPY certs/server.key /usr/local/apache2/conf/server.key                                                         0.2s
 => [8/8] COPY html/index.html /usr/local/apache2/htdocs/                                                                  0.4s
 => exporting to image                                                                                                     2.0s
 => => exporting layers                                                                                                    2.0s
 => => writing image sha256:49b83991603bc69e5944dbaf295b20a5b9d4a7b249a26f763ebc29c466af57f2

```
Yeah... i forgot to create a tag for the image
```bash
docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
<none>       <none>    49b83991603b   36 seconds ago   166MB

```
Use the image ID insted
```bash
docker run -d --name test 49b83991603b
bb4158abadc2ee0c0d670a5a377a1f3c3c87eee49b42b9c2d239aaf0cdd65110
```
and everything is fine
```bash
docker ps
CONTAINER ID   IMAGE          COMMAND              CREATED         STATUS         PORTS     NAMES
bb4158abadc2   49b83991603b   "httpd-foreground"   5 seconds ago   Up 4 seconds   80/tcp    test
```
