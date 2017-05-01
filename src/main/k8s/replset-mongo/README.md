# MongoDB Replica Set with Kubernetes on the Google Cloud Platform
This setup has the same features like the single-mongo setup but every web-app does not connect to the only MongoDB instance but to every one of the [MongoDB Replica Set](https://docs.mongodb.com/manual/replication/). The MongoDB driver at the web-app will use the primary instance for writing and the other instances will keep themselfs in sync. This allows for a better availability as there will be no downtime of the database if one instance process becomes unavailable.

## The setup
![MongoDB ReplSet instances](../../docu/k8s-example/Folie2.png)
                                 
## Create the project
1. Go to the [Google Cloud Console](https://console.cloud.google.com)
1. Create a project for this setup from the dropdown left beside the big search field. We will call it `sling-single-mongo` in this example
1. Navigate to the Container engine from the menu on the left and activate it.

## Creating the cluster
The following commmand creates a cluster of 3 g1-small nodes in the US. You find the pricing [here](https://cloud.google.com/compute/pricing#predefined_machine_types). 

    `gcloud container \
    clusters create "sling-cluster" \
    --project "sling-replset-mongo" \
    --zone "us-central1-a" \
    --machine-type "g1-small" \
    --num-nodes "4" \
    --network "default"`

## Or authenticate with an existing Google Cluster
    gcloud container clusters get-credentials sling-cluster \
      --project "sling-replset-mongo" \
      --zone "us-central1-a"
  
## The concrete Kubernetes setup
You are about to create the following Kubernetes setup:
![MongoDB ReplSet instances](../../docu/k8s-example/Folie4.png)

It is based on the single-mongo setup. The `../web-service.yml` is the same and the `web-deployment.yml` only differs in using more than one MongoDB hostnames. It's the DNS names as they are provided by the Service in the `mongo-statefulset.yaml`. This time no Deployment but a [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) is used. It mainly differs by an ordinal index that is added to the Pod name and together with the [Headless Service](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services) it provides the DNS names for the MongoDB pods that are used in the `web-deployment.yml`. The `name=mongo` Pods also have the [Sidecar](https://github.com/cvallance/mongo-k8s-sidecar/tree/master/example/StatefulSet) image inside. This is a NodeJS server that uses the [Kubernetes API](https://kubernetes.io/docs/api-reference/v1.5/#statefulset-v1beta1) to join or remove MongoDB instances to or from a MongoDB Replica Set when the according MongoDB Pods have been created or removed.

## Applying the setup within the `replset-mongo` folder
### Apply the persistent drive
If you would like to have a fast and more expensive setup call `kubectl apply -f googlecloud_ssd.yaml` and change the `storage-class` in `mongo-statefulset.yaml` from `slow` to `fast`.

### Apply the Service and StatefulSet (with specified persistent drive)
`kubectl apply -f mongo-statefulset.yaml`

### Apply the web-app Deployment with its Pods after the DB Pods are ready
`kubectl apply -f web-deployment.yml`

### Apply the load balancer service
`kubectl apply -f ../web-service.yml`

## Scaling, new application versions, cleanup...
These aspects work the same as in [the single-mongo setup](../single-mongo/README.md#get-public-ip). Just use `sling-replset-mongo` as project.

