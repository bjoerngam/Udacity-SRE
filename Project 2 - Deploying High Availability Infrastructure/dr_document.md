# Infrastructure

## AWS Zones
For zone1: us-east-2a, us-east-2b
For zone2: us-west-1b, us-west-1c

## Servers and Clusters

### Table 1.1 Summary
| Asset      | Purpose           | Size                                                                   | Qty                                                             | DR                                                                                                           |
|------------|-------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| i-0cceff027d8f41a40 |  EC2 instance | t3 medium | 1 | Yes, has to be replicated
| i-0cceff034032253a40 |  EC2 instance | t3 medium | 1 | Yes, has to be replicated
| i-0d402a5493e10b47a | Ubuntu Web | t3 micro | 1 | Yes, has to be replicated (our webserver)
| udacity-tf-bgam | S3 filestore for the tfstate file | S3 | 1 | Yes for storing the tfstate, re-create S3 store
| udacity-tf-bgam-west | S3 filestore for the tfstate file | S3 | 1 | Yes for storing the tfstate, re-create S3 store
| udacity-cluster | EKS cluster with 2 notes | EKS cluster | 1 | Yes for serving the requests
| a384a6fde96f24ed1aba5f705b7e4066 | ALB | ALB | 1 | Yes for serving the requests
| udacity-db-cluster | RDS cluster (mysql_aurora.1.19.1) with one read replica | RDS | Yes otherwise we cannot store data into our db
| udcity-project | VPC for our enviornment | VPC | No, will be recreated after redeployment
| sgr-07e7ea44993684ee1 | Security group ingress port 22 | Security group | Yes, we need this for SSH access
| sgr-0b3c08c0bb681e833 | Security group ingress port 80 | Security group | Yes, we need this for the HTTPD server
| sgr-0c6b12c8410dcdd26 | Security group ingress port 9100 | Security group | Yes, we need this for the Flusk client at the EC2 instance.
|  udacity-bgam | AMI image | AMI | Yes, this is the baseline for our Linux instances.

### Descriptions
Our cloud enviornment has one EKS cluster with a min. of two nodes and three EC2 instances, we are using a customed Linux IAM for the instances. Three different security groups are assigned to the EC2 instance. There is one ALB in place for our EC2 insances. We have also one RDS cluster with one read replica. 

## DR Plan
### Pre-Steps:
1. You have to change the region at the file: main.tf file under "general" and also the AZs at the VPC module.
2. You have to change the AMI name at the EC2.tf file.
3. You have to create a new S3 bucket at the new region.
4. You have to change at the _config.tf file the region and add the new S3 bucket name.


## Steps:
You won't actually perform these steps, but write out what you would do to "fail-over" your application and database cluster to the other region. Think about all the pieces that were setup and how you would use those in the other region
1. Create a new folder: "zone 3".
2. Copy all of the files from: "zone 1" to: "zone 3". 
3. Look the AZs up in the region.
4. Change the files:
4.1 ec2.tf
4.2 _config.tf
4.3 main.tf
5. change into the new zone directory
6. terraform init
7. terraform apply
8. In case you running into some issues with the EKS cluster setup:
8.1 aws eks --region <region> update-kubeconfig --name udacity-cluster
8.2 kubectl config use-context <cluster_name>
8.3 kubectl get pods --all-namespaces
8.4 kubectl create namespace monitoring

