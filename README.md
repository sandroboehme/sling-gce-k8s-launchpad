# Sling DevOps with Kubernetes example
## Prerequisites
### gloud command
https://cloud.google.com/sdk/
### kubectl command
https://kubernetes.io/docs/tasks/kubectl/install/
### docker command
https://docs.docker.com/engine/installation/
### getting the code
https://github.com/sandroboehme/sample-launchpad
### building the launchpad
mvn clean process-resources slingstart:prepare-package slingstart:package docker:build docker:push -Dmongodb.host=mongo

