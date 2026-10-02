# Day 49: Deploy Applications with Kubernetes Deployments

## Onjective

The Nautilus DevOps team is delving into Kubernetes for app management. One team member needs to create a deployment following these details:


Create a deployment named `httpd` to deploy the application `httpd` using the image `httpd:latest` (ensure to specify the tag)

## Solution

To create a deployment we need to use `create deployment`
Example:
```bash
kubectl create deployment <my-dep> --image=<container-image>
```
Now change to the right setting.
```bash
kubectl create deployment httpd --image=httpd:latest
# Output
deployment.apps/httpd created
```
Verify the deployment status.
```bash
kubectl get deployments
# Output
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
httpd   1/1     1            1           44s
```
Last verify the pod status.
```bash
kubectl get pods
# Output
NAME                     READY   STATUS    RESTARTS   AGE
httpd-6c755866c7-4cc4w   1/1     Running   0          64s
```
