# Infrastructure

## AWS Zones
For zone1: us-east-2a, us-east-2b, us-east-2c
For zone2: us-west-1b, us-west-1c

## Servers and Clusters

### Table 1.1 Summary
| Asset                            | Purpose                                                 | Size           | Qty | DR                                                                   |
|----------------------------------|---------------------------------------------------------|----------------|-----|----------------------------------------------------------------------|
| i-0cceff027d8f41a40              | EC2 instance                                            | t3 medium      | 1   | Yes, has to be replicated. Runs in a single AZ.                      |
| i-0cceff034032253a40             | EC2 instance                                            | t3 medium      | 1   | Yes, has to be replicated. Runs in a single AZ.                      |
| Ubuntu Web                       | Web Server                                              | t3 micro       | 1   | Yes, has to be replicated (our webserver) Runs in a single AZ.       |
| udacity-tf-bgam                  | S3 filestore for the tfstate file                       | S3             | 1   | Yes for storing the tfstate, re-create S3 store, Runs in a single AZ |
| udacity-tf-bgam-west             | S3 filestore for the tfstate file                       | S3             | 1   | Yes for storing the tfstate, re-create S3 store, Runs in a single AZ |
| udacity-cluster                  | EKS cluster with 2 notes                                | EKS cluster    | 1   | Yes for serving the requests, Runs in a single AZ                    |
| a384a6fde96f24ed1aba5f705b7e4066 | ALB                                                     | ALB            | 1   | Yes for serving the requests, Runs in a single AZ                    |
| udacity-db-cluster               | RDS cluster (mysql_aurora.1.19.1) with one read replica | db.t2.small    | 1   | Yes otherwise we cannot store data into our db, multi AZ             |
| udacity-project                  | VPC for our enviornment                                 | VPC            | 1   | No, will be recreated after redeployment                             |
| sgr-07e7ea44993684ee1            | Security group ingress port 22                          | Security group | 1   | Yes, we need this for SSH access                                     |
| sgr-0b3c08c0bb681e833            | Security group ingress port 80                          | Security group | 1   | Yes, we need this for the HTTPD server                               |
| sgr-0c6b12c8410dcdd26            | Security group ingress port 9100                        | Security group | 1   | Yes, we need this for the Flusk client at the EC2 instance.          |
| udacity-bgam                     | AMI image                                               | AMI            | 1   | Yes, this is the baseline for our Linux instances.                   |
| udacity                          | SSH key                                                 | N/A            | 1   | SSH key for the instances.                                           |
| Monitoring                       | Monitoring plattform for the web application            | N/A            | 1   | Yes, we need this also in our backup zone.                           |


### Descriptions
- Ubuntu web server hosts flask web application.
- RDS cluster holds the web application data.
- EKS nodes hosts monitoring platform to monitor the web application.
- ALB handles the requests.

## DR Plan
### Pre-Steps:
1. Ensure you have alligned configurations at both zones.
2. Deploy the 2nd zone via Terraform.


## Steps:
You won't actually perform these steps, but write out what you would do to "fail-over" your application and database cluster to the other region. Think about all the pieces that were setup and how you would use those in the other region
1. Use DNS (Route53) for doing the failover to the 2nd zone (backup zone). Maybe latency routing could be an option.
2. Promote the first RDS instance to a read replica. (Manual failover to the backup zone). 
