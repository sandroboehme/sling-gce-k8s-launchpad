# Sling DevOps with Kubernetes example
## Prerequisites
### gloud command
https://cloud.google.com/sdk/

### kubectl command
gcloud components install kubectl

### docker command
https://docs.docker.com/engine/installation/

### getting the code
https://github.com/sandroboehme/sample-launchpad

### building the launchpad
`mvn clean process-resources slingstart:prepare-package slingstart:package docker:build docker:push -Dmongodb.host=mongo`

## Creating the cluster
    gcloud container \
    clusters create "sling-cluster-small" \
    --project "sling-devops-test" \
    --zone "us-central1-a" \
    --machine-type "g1-small" \
    --num-nodes "2" \
    --network "default"
  
## Creating the MongoDB Service
### Creating the disk
    gcloud compute disks create \
    --project "sling-devops-test" \
    --zone "us-central1-a" \
    --size 200GB \
    mongo-disk
    
### Authenticate with Google
```gcloud container clusters get-credentials sling-cluster-small \
  --project "sling-devops-test" \
  --zone "us-central1-a"
  
cd src/main/k8s/
kubectl apply -f db-controller.yml
kubectl apply -f db-service.yml
kubectl apply -f web-controller.yml
kubectl apply -f web-service.yml
```

## Get IP address to access the site
    kubectl describe service -l name=web
The one next to `LoadBalancer Ingress`
    http://<ip-address>/mynode.test

## Scale up
    gcloud container \
      clusters resize "sling-cluster-small" \
      --project "sling-devops-test" \
      --zone "us-central1-a" \
      --size 3

    kubectl scale --replicas=2 deployment web-deployment

## Pause project to avoid costs from Google
    kubectl scale --replicas=0 deployment web-deployment

    gcloud container \
      clusters resize "sling-cluster-small" \
      --project "sling-devops-test" \
      --zone "us-central1-a" \
      --size 0
  
## Cleanup
    kubectl scale --replicas=0 deployment mongo-deployment
    kubectl scale --replicas=0 deployment web-deployment
    gcloud compute disks delete \
      --project "sling-devops-test" \
      --zone "us-central1-a" \
      mongo-disk
  
    clusters delete "sling-cluster-small" \
      --project "sling-devops-test" \
      --zone "us-central1-a" 
