# Day 52: Revert Deployment to Previous Version in Kubernetes

## Objective

Earlier today, the Nautilus DevOps team deployed a new release for an application. However, a customer has reported a bug related to this recent release. Consequently, the team aims to revert to the previous version.


There exists a deployment named `nginx-deployment`; initiate a rollback to the previous revision.

## Solution
Well, since I have another repo with this lab, this one will be simpler. If you want a version that shows the rollout changes, you can go to Kubernetes L1.

We're going tu use `rollout undo` followed by our deployment to revert the changes.
```bash
kubectl rollout undo deployment/nginx-deployment
# Output
deployment.apps/nginx-deployment rolled back
```

If you'd like, you can even check the status of the roolout
```bash
kubectl rollout status deployment/nginx-deployment
# Output
deployment "nginx-deployment" successfully rolled out
```
