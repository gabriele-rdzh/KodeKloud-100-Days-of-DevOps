# Day 51: Execute Rolling Updates in Kubernetes

## Objective

An application currently running on the Kubernetes cluster employs the nginx web server. The Nautilus application development team has introduced some recent changes that need deployment. They've crafted an image `nginx:1.19` with the latest updates.


Execute a rolling update for this application, integrating the `nginx:1.19` image. The deployment is named `nginx-deployment`.

Ensure all pods are operational post-update.

## Solution

To update the application, we first need to know the name of the container. To do this, we can us `describe`.
```bash
kubectl describe deployment nginx-deployment
# Output
Name:                   nginx-deployment
...
  Containers:
   nginx-container:
    Image:         nginx:1.16
    ...
```

Now that we have the name, we can set the updated image as follows
```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.19
# Output
deployment.apps/nginx-deployment image updated
```

We can check the status of the update
```bash
kubectl rollout status deployment/nginx-deployment
# Output
deployment "nginx-deployment" successfully rolled out
```

Finally, we verify that the pods are running. We can also verify that the container image has changed.
```bash
kubectl get pods
# Output
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-6655dc8cfb-cq5sh   1/1     Running   0          53s
nginx-deployment-6655dc8cfb-czzcg   1/1     Running   0          49s
nginx-deployment-6655dc8cfb-lckkf   1/1     Running   0          49s
```
