# Day 47: Docker Python App

## Objective

A python app needed to be Dockerized, and then it needs to be deployed on `App Server 3`. We have already copied a `requirements.txt` file (having the app dependencies) under `/python_app/src/` directory on App Server 3. Further complete this task as per details mentioned below:



1. Create a `Dockerfile` under `/python_app directory`:

   - Use any `python` image as the base image.
   - Install the dependencies using `requirements.txt file`.
   - Expose the port `6100`.
   - Run the `server.py` script using `CMD`.

2. Build an image named `nautilus/python-app` using this Dockerfile.

3. Once image is built, create a container named `pythonapp_nautilus`:

   - Map port `6100` of the container to the host port `8097`.

4. Once deployed, you can test the app using curl command on `App Server 3`.

## Solution

First, let's create the Dockerfile as follows.

```Dockerfile
FROM python:alpine

WORKDIR /app

COPY src/ .

RUN pip install --no-cache-dir -r requirements.txt

EXPOSE 6100

CMD ["python", "server.py"]
```

Now let's create the image using the Dockerfile

```bash
docker build -t nautilus/python-app .

docker images
# Output
REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
nautilus/python-app   latest    487ab059e7ef   4 minutes ago   62.1MB
```

Then the copntainer with the specified name and the port mapping

```bash
docker run -d --name pythonapp_nautilus -p 8097:6100 nautilus/python-app

docker ps
# Output
CONTAINER ID   IMAGE                 COMMAND              CREATED         STATUS         PORTS                                       NAMES
a9a77b300f74   nautilus/python-app   "python server.py"   2 minutes ago   Up 2 minutes   0.0.0.0:8097->6100/tcp, :::8097->6100/tcp   pythonapp_nautilusdocker
```
and finally, we use curl to check the running server

```bash
curl http://localhost:8097/
Welcome to xFusionCorp Industries!
```
