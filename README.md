# re-firebreak-k8s

These notes document the firebreak project to learn some Kubernetes (k8s)

# Aims

1. Produce a ‘thing’ to show

1. Learn stuff about K8s

2. Only use code to build the ‘thing’

3. Build a thing which can be deployed to

4. Document what we do and lessons learnt

5. Have fun!

# Team

Members are:

1. David McDonald

2. David Pye

3. Michael (Miki) Mokrysz

4. Philip Potter

5. Stephen Ford

6. Frederic Francois

# Requirements

Requirements are:

1. AWS programmatic credentials with sufficient privileges to create VPC, ec2, loadBalancer, s3, etc

2. [kops](https://github.com/kubernetes/kops) to deploy the k8s cluster on AWS infrastructure.

  Example to install `kops` on mac os using `brew`:
  ```
  brew install kops
  ```

# Project Details

## Stage 1

Build a K8s cluster

Ref:

1. [using kops to k8s on k8s](https://medium.com/containermind/how-to-create-a-kubernetes-cluster-on-aws-in-few-minutes-89dda10354f4)

## Stage 2

Build & Deploy a hello world app to the ‘thing’

## Stage 3

Deploy a CI/CD tool to the ‘thing’


# Refs
*https://medium.com/containermind/how-to-create-a-kubernetes-cluster-on-aws-in-few-minutes-89dda10354f4

*https://kubernetes.io/docs/user-journeys/users/application-developer/foundational/#section-1

*https://github.com/99designs/aws-vault
