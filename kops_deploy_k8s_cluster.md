# Deploying k8s cluster using kops on AWS

## Deploy

1. export your AWS credentials:
  ```
  export AWS_ACCESS_KEY_ID=<AWS_ACCESS_KEY_ID_VALUE>
  export AWS_SECRET_ACCESS_KEY=<AWS_SECRET_ACCESS_KEY_VALUE>
  ```
  where `<AWS_ACCESS_KEY_ID_VALUE>` and `<AWS_SECRET_ACCESS_KEY_VALUE>` are the retrieved values from your AWS IAM webpage

2. create an AWS S3 bucket for kops to store its state:
  ```
  bucket_name=gds-kops-state-store
  aws s3api create-bucket \
  --bucket ${bucket_name} \
  --region eu-west-2 \
  --create-bucket-configuration \
  LocationConstraint=eu-west-2
  ```
  This will create a bucket with name `gds-kops-state-store` in AWS region `eu-west-2`, i.e London

3. enable versioning for the AWS S3 bucket created above:
   ```
   aws s3api put-bucket-versioning --bucket ${bucket_name} --versioning-configuration Status=Enabled
   ```

4. export the `KOPS_STATE_STORE`:
  ```
  export KOPS_STATE_STORE=s3://${bucket_name}
  ```

5. create a kops secret to store the SSH public key to be used for AWS EC2 instances:
  ```
  kops create secret --name gds.k8s.local sshpublickey admin -i ~/.ssh/id_rsa.pub
  ```

6. create a kops cluster definition:
  ```
  kops create -f gds.k8s.local.yaml
  ```
  which will create a k8s cluster definition made of 1 master and 2 worker nodes in region in AWS region `eu-west-2`

7. deploy the k8s cluster:
  ```
  kops update cluster gds.k8s.local --yes
  ```

8. validate the k8s cluster, i.e. verify the state of the cluster. You may have to repeat this command until the cluster is in state ready:
  ```
  kops validate cluster

  ```

  An example of an output when the cluster is ready is:
  ```
  Using cluster from kubectl context: gds.k8s.local

  Validating cluster gds.k8s.local

  INSTANCE GROUPS
  NAME			ROLE	MACHINETYPE	MIN	MAX	SUBNETS
  master-eu-west-2a	Master	m4.large	1	1	eu-west-2a
  nodes			Node	t2.medium	2	2	eu-west-2a

  NODE STATUS
  NAME						ROLE	READY
  ip-172-20-49-138.eu-west-2.compute.internal	master	True
  ip-172-20-56-135.eu-west-2.compute.internal	node	True
  ip-172-20-63-93.eu-west-2.compute.internal	node	True

  Your cluster gds.k8s.local is ready
  ```

## Deploy k8s dashboard:

To deploy the k8s dashboard and access it via a local web browser, do:

1. deploy the dashboard service and deployments:
  ```
  kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
  ```

2. get the secret token to be used to access the dashboard:
  ```
  kops get secrets kube --type secret -oplaintext
  ```

4. in a terminal, run and keep alive the kubectl proxy which will allow access to the dashboard from your local browser:
  ```
  kubectl proxy
  ```

5. browse to the following link:
  ```
  http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login
  ```
  and log in using the token that you retrieved in step 2


## Modifying the cluster

You can modify the cluster by doing:

1. modifying the cluster definition
  ```
  kops edit cluster --name gds.k8s.local
  ```

2. apply the new cluster definition:
  ```
  kops update cluster --name gds.k8s.local --yes
  ```

### References

1. [using kops to k8s on k8s](https://medium.com/containermind/how-to-create-a-kubernetes-cluster-on-aws-in-few-minutes-89dda10354f4)
