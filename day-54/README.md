# Day 54: Kubernetes Shared Volumes

## Objective

We are working on an application that will be deployed on multiple containers within a pod on Kubernetes cluster. There is a requirement to share a volume among the containers to save some temporary data. The Nautilus DevOps team is developing a similar template to replicate the scenario. Below you can find more details about it.



1. Create a pod named `volume-share-xfusion`.


2. For the first container, use image `fedora` with `latest` tag only and remember to mention the tag i.e `fedora:latest`, container should be named as `volume-container-xfusion-1`, and run a `sleep` command for it so that it remains in running state. Volume `volume-share` should be mounted at path `/tmp/official`.


3. For the second container, use image `fedora` with the `latest` tag only and remember to mention the tag i.e `fedora:latest`, container should be named as `volume-container-xfusion-2`, and again run a `sleep` command for it so that it remains in running state. Volume `volume-share` should be mounted at path `/tmp/cluster`.


4.Volume name should be `volume-share` of type `emptyDir`.


5. After creating the pod, exec into the first container i.e `volume-container-xfusion-1`, and just for testing create a file `official.txt` with the content `Welcome to xFusionCorp Industries` under the mounted path of first container i.e `/tmp/official`.


6. The file `official.txt` should be present under the mounted path `/tmp/cluster` on the second container `volume-container-xfusion-2` as well, since they are using a shared volume.

## Solution

To create a pod as requested in steps 1 throught 4, we'll use the following YAML; for the containers, the structure is basically the same —in fact, the only differences are in the name and the mountPath(and just a little)— and we'll also specify the shared volume a the same level as the containers
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-xfusion
spec:
  containers:
  - name: volume-container-xfusion-1
    image: fedora:latest
    command: ["sleep", "3600"]
    volumeMounts:
    - name: volume-share
      mountPath: /tmp/official
  - name: volume-container-xfusion-2
    image: fedora:latest
    command: ["sleep", "3600"]
    volumeMounts:
    - name: volume-share
      mountPath: /tmp/cluster
  volumes:
  - name: volume-share
    emptyDir: {}
```
Now that the YAML is ready, we'll proceed to create the pod
```bash
kubectl apply -f pod.yaml 
pod/volume-share-xfusion created
```
we checked
```bash
kubectl get pods
NAME                   READY   STATUS    RESTARTS   AGE
volume-share-xfusion   2/2     Running   0          21s
```
Now, with the following command, we're going to enter to `volume-container-xfusion-1` to create our test file.
```bash
kubectl exec -it volume-share-xfusion -c volume-container-xfusion-1 -- /bin/sh
sh-5.3# echo "Welcome to xFusionCorp Industries" > /tmp/official/official.txt
sh-5.3# exit
```
Now, with a simple `cat` command in `volume-container-xfusion-2`, we can check if the file is on the shared volume. We could also use `ls` first, but since we're confident and you won't be able to tell if I'm wrong or not, we're just going to use `cat`
```bash
kubectl exec -it volume-share-xfusion -c volume-container-xfusion-2 -- cat /tmp/cluster/official.txt
Welcome to xFusionCorp Industries
```
it worked :3
