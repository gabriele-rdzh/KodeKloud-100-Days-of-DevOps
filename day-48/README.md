# Day 48: Deploy Pods in Kubernetes Cluster

## Objective

The Nautilus DevOps team is diving into Kubernetes for application management. One team member has a task to create a pod according to the details below:


1. Create a pod named `pod-httpd` using the `httpd` image with the `latest` tag. Ensure to specify the tag as `httpd:latest`.

2. Set the app label to `httpd_app`, and name the container as `httpd-container`.

## Solution

Since we've already done a similar lab before, this time we're going to write our pod.yaml differently.
```bash
kubectl run pod-httpd --image=httpd:latest --labels="app=httpd_app" --containers="name=httpd-container" --dry-run=client -o yaml > pod.yaml
```

so the pod.yaml is almost ready; we just need to change the container name to the correct one, since the pod name is also used as the container name.
```bash
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: httpd_app
  name: pod-httpd
spec:
  containers:
  - image: httpd:latest
    name: httpd-container
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

Now let's proceed to create or pod
```bash
kubectl apply -f pod.yaml 
pod/pod-httpd created
```

and we're done
```bash
kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
pod-httpd   1/1     Running   0          19s
```

