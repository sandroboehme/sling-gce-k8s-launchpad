
# Sling DevOps with Kubernetes on the Google Cloud Platform
The goal of this project is, to provide a reusable production ready Sling infrastructure and to optimize and enhance it over time.
That's why bug reports, fixes, enhancements and comments are very much appreciated.
I'm more of a developer than an ops guy. So please validate for yourself as well if this project works for you.

## The status
*In Development*
There are missing main things like DB backup and recovery, securing Sling and implementing the suggestions from the MongoDB startup logs. But also main cost optimizations like removing the use of a load balancer, use of [GCE preemptiveness](https://cloud.google.com/compute/docs/instances/preemptible), thinking about autoscaling to remove not needed cluster nodes ...

## Prerequisites

### command line tools
- gloud: 
https://cloud.google.com/sdk/
- kubectl: 
gcloud components install kubectl

### building and publishing the image
#### Setup the authentication to e.g. Docker Hub
The different ways to do it can be found in [the fabric8 docu](https://dmp.fabric8.io/#authentication). The one that worked for me was, adding the following snippet to `~/.m2/settings.xml`:
`<settings>
  <servers>
    <server>
      <id>docker.io</id>
      <username>sandroboehme</username>
      <password>[your-password]</password>
    </server>
  </servers>
</settings>`

#### The command 
mvn clean process-resources slingstart:prepare-package slingstart:package docker:build docker:push

## This project currently supports two configurations.
1. Single MongoDB instance. It allows to horizontally scale the web-app in Jetty and to release a new version without downtime. It allows only one MongoDB image running. But it will be automatically restarted if the process dies. Check out more in the [src/main/k8s/single-mongo subfolder](src/main/k8s/single-mongo/README.md)
(src/main/docu/k8s-example/Folie1.png "Single MongoDB instance")
1. With MongoDB ReplSet instances.  Check out more in the [src/main/k8s/replset-mongo subfolder](src/main/k8s/replset-mongo/README.md)
(src/main/docu/k8s-example/Folie2.png "MongoDB ReplSet instances")
