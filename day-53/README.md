# Day 53: Resolve VolumeMounts Issue in Kubernetes

## Objective

We encountered an issue with our Nginx and PHP-FPM setup on the Kubernetes cluster this morning, which halted its functionality. Investigate and rectify the issue:



The pod name is `nginx-phpfpm` and configmap name is `nginx-config`. Identify and fix the problem.


Once resolved, copy `/home/thor/index.php` file from the `jump host` to the `nginx-container` within the nginx document root. After this, you should be able to access the `website` using Website button on the top bar.

## Solution
First, lets verify that the pods are running and that there are no errors in the events section of the `describe` command
```bash
kubectl get pods -o wide
NAME           READY   STATUS    RESTARTS   AGE     IP          NODE        NOMINATED NODE   READINESS GATES
nginx-phpfpm   2/2     Running   0          5m39s   10.22.0.9   jump-host   <none>           <none>
```
```bash
kubectl describe pod nginx-phpfpm
Name:             nginx-phpfpm
Namespace:        default
Priority:         0
Service Account:  default
Node:             jump-host/10.244.221.3
Start Time:       Wed, 07 Oct 2026 05:45:51 +0000
Labels:           app=php-app
Annotations:      <none>
Status:           Running
IP:               10.22.0.9
IPs:
  IP:  10.22.0.9
Containers:
  php-fpm-container:
    Container ID:   containerd://3ae4088f5707c3efe8b2d54b4c998ab267dc84ff6e81c57e1b44c4769a46ef02
    Image:          php:7.2-fpm-alpine
    ...
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  6m2s   default-scheduler  Successfully assigned default/nginx-phpfpm to jump-host
  Normal  Pulling    6m2s   kubelet            Pulling image "php:7.2-fpm-alpine"
  Normal  Pulled     6m     kubelet            Successfully pulled image "php:7.2-fpm-alpine" in 1.583s (1.583s including waiting). Image size: 30733687 bytes.
  Normal  Created    6m     kubelet            Created container: php-fpm-container
  Normal  Started    6m     kubelet            Started container php-fpm-container
  Normal  Pulling    6m     kubelet            Pulling image "nginx:latest"
  Normal  Pulled     5m57s  kubelet            Successfully pulled image "nginx:latest" in 2.696s (2.697s including waiting). Image size: 63873678 bytes.
  Normal  Created    5m57s  kubelet            Created container: nginx-container
  Normal  Started    5m57s  kubelet            Started container nginx-container
```
As we can see, there are no errors

Let's check the ConfigMap
```bash
kubectl get configmap nginx-config -o yaml
apiVersion: v1
data:
  nginx.conf: |
    events {
    }
    http {
      server {
        listen 8099 default_server;
        listen [::]:8099 default_server;

        # Set nginx to serve files from the shared volume!
        root /var/www/html;
        index  index.html index.htm index.php;
        server_name _;
        location / {
          try_files $uri $uri/ =404;
        }
        location ~ \.php$ {
          include fastcgi_params;
          fastcgi_param REQUEST_METHOD $request_method;
          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
          fastcgi_pass 127.0.0.1:9000;
        }
      }
    }
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"nginx.conf":"events {\n}\nhttp {\n  server {\n    listen 8099 default_server;\n    listen [::]:8099 default_server;\n\n    # Set nginx to serve files from the shared volume!\n    root /var/www/html;\n    index  index.html index.htm index.php;\n    server_name _;\n    location / {\n      try_files $uri $uri/ =404;\n    }\n    location ~ \\.php$ {\n      include fastcgi_params;\n      fastcgi_param REQUEST_METHOD $request_method;\n      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;\n      fastcgi_pass 127.0.0.1:9000;\n    }\n  }\n}\n"},"kind":"ConfigMap","metadata":{"annotations":{},"name":"nginx-config","namespace":"default"}}
  creationTimestamp: "2026-10-07T05:45:50Z"
  name: nginx-config
  namespace: default
  resourceVersion: "1313"
  uid: c5e2f406-3505-49c9-9e02-419bd0570280
```
As we can see, the root path is not hte same as the mouthPath of the php-fpm container, so let's change it.

let's "save" the pod configuration in a YAML file so we can edit it and rtecreate the pod
```bash
kubectl get pod nginx-phpfpm -o yaml > pod.yaml
```
```YAML
apiVersion: v1
kind: Pod
metadata:
  ...
  name: nginx-phpfpm
  namespace: default
  resourceVersion: "1331"
  uid: b3f46279-3c8b-4870-8457-d38acf2a9896
spec:
  containers:
  - image: php:7.2-fpm-alpine
    imagePullPolicy: IfNotPresent
    name: php-fpm-container
    ...
    volumeMounts:
    - mountPath: /var/www/html
      name: shared-files
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-s7mv6
      readOnly: true
  - image: nginx:latest
    imagePullPolicy: Always
    name: nginx-container
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/www/html
      name: shared-files
    - mountPath: /etc/nginx/nginx.conf
      name: nginx-config-volume
      subPath: nginx.conf
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-s7mv6
      readOnly: true
  ...
```
Then we'll delete the pod se we can recrate it correctly 
```bash
kubectl delete pod nginx-phpfpm
# Output
pod "nginx-phpfpm" deleted from default namespace
```
```bash
kubectl get pods -o wide
# Output
No resources found in default namespace.
```
```bash
kubectl apply -f pod.yaml
# Output 
pod/nginx-phpfpm created
```
```bash
kubectl get pods -o wide
# Output
NAME           READY   STATUS    RESTARTS   AGE   IP           NODE        NOMINATED NODE   READINESS GATES
nginx-phpfpm   2/2     Running   0          40s   10.22.0.10   jump-host   <none>           <none>
```
finally, we copy the index.php file, and we can see that the page is now working normally
```bash
kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
```
<img width="1517" height="857" alt="image" src="https://github.com/user-attachments/assets/bb62f1e4-45c5-4ef1-bb88-0643cbe4254d" />
