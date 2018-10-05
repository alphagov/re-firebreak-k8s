# Continuous Integration and Delivery (CI/D): GoCD

This document describe how to install GoCD on a Kubernetes cluster and run a set of pipelines to introduce the GoCD tool.

## Prerequisites

The following prerequisites are required:

1. have a k8s cluster available. One way of obtaining this is to deploy a k8s cluster in AWS as described [here](kops_deploy_k8s_cluster.md)

2. an account for the dockerhub registry. You can create one [here](https://hub.docker.com)

## Installation and Set of Pipelines

1. The installation instructions as well as the tutorial to run a set of pipelines are given [here](https://docs.gocd.org/current/gocd_on_kubernetes/)

2. If you have encounter any issue, please see these set of some important caveats:

  1. Installing Helm: You need to configure both an ingress controller and correct authentication settings before Helm can be installed and operated successfully. See further details [here](https://docs.gocd.org/current/gocd_on_kubernetes/gocd_helm_chart/configure_cluster.html#option-3-configure-kops)

  2. Configuring Artifact Store: You need your own Docker Hub credentials for the artifact store

  3. Accessing the GoCD server: You should access the GoCD server by doing:
      ```
      kubectl get ingress --namespace gocd gocd-server
      ```
     and using the URL link

  4. `build_and_publish_image` pipeline, the bash command text box should have:
     ```
     -c
     docker build -t $DOCKERHUB_USERNAME/bulletin-board:$GO_PIPELINE_LABEL . -f Dockerfile.application
     ```

  5. `deploy_app_to_cluster` pipeline, the bash command text box should have:
     ```
     -c
     PLUGIN_FILENAME=cd.go.artifact.docker.registry.json ./create_json.sh
     ```

  6. Getting the `KUBE_TOKEN` value is done by:

     1. retrieving the token name:
        ```
        kubectl describe sa default --namespace kube-system
        ```

     2. retrieving the token value based on the token name:
        ```
        kubectl describe secrets <token_name> --namespace kube-system
        ```  

  7. Accessing the deployed application is done by:

     1. getting the URL of the application:
        ```
        kubectl get ingress bulletin-board-ingress
        ```

     2. browse to the URL in a browser, you may encounter some certificate error due to the deployed application not using a TLS certificate from a recognised Certificate Authority.
