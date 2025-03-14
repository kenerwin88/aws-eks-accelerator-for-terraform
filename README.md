# aws-eks-accelerator-for-terraform

# Main Purpose
The main purpose of this project is to provide a Terraform framework to help you get started on deploying **EKS Clusters** in multi-tenant environments using Hashicorp Terraform with AWS and Helm Providers. 

# Overview
EKS Terraform accelerator module helps you to provision **EKS clusters**, **Managed node groups** with **on-demand** and **spot instances**, **Fargate profiles** and all the necessary plugins/addons for EKS cluster. Terraform **Helm provider** is used to deploy the common Kubernetes add-ons with publicly available [Helm Charts](https://artifacthub.io/). This project leverages the official [terraform-aws-eks](https://github.com/terraform-aws-modules/terraform-aws-eks) module to create EKS Clusters. This framework helps you to design and create EKS clusters for different environments in various AWS accounts across multiple regions with a **unique Terraform configuration and state file** for each EKS cluster. 

* Top level **live** folder contains the configuration setup for each cluster. Each folder under `live/<region>/application` represents an EKS cluster environment(e.g., dev, test, load etc.). 
This folder contains `backend.conf` and `base.tfvars` which are used to create a unique Terraform state for each cluster environment.
Terraform backend configuration can be updated in `backend.conf` and cluster common configuration variables in `base.tfvars`

* **source** folder contains main driver file `main.tf`
* **modules** folder contains all the AWS resource modules
* **helm** folder contains all the Helm chart modules
* **examples** folder contains sample template files with `base.tfvars` which can be used to deploy clusters with multiple add-on options

# EKS Cluster Deployment Options
This module helps you to provision the following EKS resources

        1. VPC, Subnets(Public and Private) and VPC endpoints for fully private EKS Clusters (Optional)
        2. EKS Cluster with multiple networking options 
           2.1 Fully private EKS Cluster
           2.2 Public + Private EKS Cluster
           2.3 Public Cluster
        3. AWS Managed Node Groups with on-demand and Spot instances, self-managed node groups and Fargate profiles
        4. AWS Managed node groups with launch templates 
        5. AWS SSM agent deployed through launch templates
        6. RBAC for Developers and Administrators with IAM roles
        7. Kubernetes Addons using Helm Charts
        8. Metrics Server
        9. Cluster Autoscaler
        10. AWS LB Ingress Controller
        11. Traefik Ingress Controller 
        12. FluentBit to Cloudwatch for Managed Node groups
        13. FluentBit to Cloudwatch for Fargate Containers

# Helm Charts Modules
Helm Chart Module within this framework allows you to deploy kubernetes apps using Terraform helm chart provider with **enabled** conditional parameter in `base.tfvars`. 

**NOTE**: Docker images used in Helm Charts requires downloading locally and push it to ECR repo for **fully private EKS Clusters**. This project provides both options of public docker hub repo and private ECR repo for all Helm chart modules. 
You can find the README for each Helm module with instructions on how to download the images from Docker Hub or third-party repos and upload it to your private ECR repo. 
For example, [ALB Ingress Controller](helm/lb_ingress_controller/README.md) for AWS LB Ingress Controller module. 

## Ingress Controller Modules
Ingress is an API object that defines the traffic routing rules (e.g. load balancing, SSL termination, path-based routing, protocol), whereas the Ingress Controller is the component responsible for fulfilling those requests.

* [ALB Ingress Controller](helm/lb_ingress_controller/README.md) can be deployed by specifying the following line in `base.tfvars` file.
**AWS ALB Ingress controller** triggers the creation of an ALB and the necessary supporting AWS resources whenever a Kubernetes user declares an Ingress resource in the cluster.
[ALB Docs](https://kubernetes-sigs.github.io/aws-load-balancer-controller/latest/)
    
    `alb_ingress_controller_enable = true`

* [Traefik Ingress Controller](helm/traefik_ingress/README.md) can be deployed by specifying the following line in `base.tfvars` file.
**Treafik is an open source Kubernetes Ingress Controller**. The Traefik Kubernetes Ingress provider is a Kubernetes Ingress controller; that is to say, it manages access to cluster services by supporting the Ingress specification. For more detials about [Traefik can be found here](https://doc.traefik.io/traefik/providers/kubernetes-ingress/)

    `traefik_ingress_controller_enable = true`
    
## Autoscaling Modules
**Cluster Autoscaler** and **Metric Server** Helm Modules gets deployed by default with the EKS Cluster. 

* [Cluster Autoscaler](helm/cluster_autoscaler/README.md) can be deployed by specifying the following line in `base.tfvars` file.
The Kubernetes Cluster Autoscaler automatically adjusts the number of nodes in your cluster when pods fail or are rescheduled onto other nodes. It's not deployed by default in EKS clusters. 
That is, the AWS Cloud Provider implementation within the Kubernetes Cluster Autoscaler controls the **DesiredReplicas** field of Amazon EC2 Auto Scaling groups. 
The Cluster Autoscaler is typically installed as a **Deployment** in your cluster. It uses leader election to ensure high availability, but scaling is one done by a single replica at a time.     

    `cluster_autoscaler_enable = true`
  
* [Metrics Server](helm/metrics_server/README.md) can be deployed by specifying the following line in `base.tfvars` file.
The Kubernetes Metrics Server, used to gather metrics such as cluster CPU and memory usage over time, is not deployed by default in EKS clusters.
    
    `metrics_server_enable = true`
    
## Logging and Monitoring
**FluentBit** is an open source Log Processor and Forwarder which allows you to collect any data like metrics and logs from different sources, enrich them with filters and send them to multiple destinations.

* [aws-for-fluent-bit](helm/aws-for-fluent-bit/README.md) can be deployed by specifying the following line in `base.tfvars` file.
AWS provides a Fluent Bit image with plugins for both CloudWatch Logs and Kinesis Data Firehose. The AWS for Fluent Bit image is available on the Amazon ECR Public Gallery. 
For more details, see [aws-for-fluent-bit](https://gallery.ecr.aws/aws-observability/aws-for-fluent-bit) on the Amazon ECR Public Gallery.                                                                                                 

    `aws-for-fluent-bit_enable = true`

* [fargate-fluentbit](helm/fargate_fluentbit) can be deployed by specifying the following line in `base.tfvars` file.
This modules ships the Fargate Continaer logs to CloudWatch

    `fargate_fluent_bit_enable = true`

# How to Deploy

## Pre-requisites:
Ensure that you installed the following tools in your Mac or Windows Laptop before start working with this module and run Terraform Plan and Apply

    1. [aws cli] (https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
    2. [aws-iam-authenticator] (https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html)
    3. [kubectl] (https://kubernetes.io/docs/tasks/tools/)
    4. wget 
    
## Deployment Steps
The following steps walks you through the deployment of example [DEV cluster](live/preprod/eu-west-1/application/dev/base.tfvars) configuration. This config deploys a private EKS cluster with public and private subnets. 
Two managed worker nodes with On-demand and Spot instances along with one fargate profile for default namespace placed in private subnets. ALB placed in Public subnets created by LB Ingress controller. 
It also deploys few kubernetes apps i.e., LB Ingress Controller, Metrics Server, Cluster Autoscaler, aws-for-fluent-bit CloudWatch logging for Managed node groups, FluentBit CloudWatch logging for Fargate etc. 

### Provision VPC (optional) and EKS cluster with selected Helm modules
   
#### Step1: Clone the repo using the command below

    $ git clone https://github.com/aws-samples/aws-eks-accelerator-for-terraform.git

#### Step2: Update base.tfvars file

Update `~/aws-terraform-eks/live/preprod/eu-west-1/application/dev/base.tfvars` file with the instructions specified in the file (OR use the default values). You can choose to use an existing VPC ID and Subnet IDs or create a new VPC and subnets by providing CIDR ranges in `base.tfvars` file

####  Step3: Update Terraform backend config file

Update `~/aws-terraform-eks/live/preprod/eu-west-1/application/dev/backend.conf` with your local directory path. [state.tf](source/state.tf) file contains backend config. 
Local terraform state backend config variables
    
    path = "local_tf_state/ekscluster/preprod/application/dev/terraform-main.tfstate"

It's highly recommended to use remote state in S3 instead of using local backend. The following variables needs filling for S3 backend.  

    bucket = "<s3 bucket name>"
    region = "<aws region>"
    key    = "ekscluster/preprod/application/dev/terraform-main.tfstate"
        
#### Step4: Assume IAM role before creating a EKS cluster. 
This role will become the Kubernetes Admin by default.
        
    $ aws-mfa --assume-role  arn:aws:iam::<ACCOUNTID>:role/<IAMROLE>

#### Step5: Run Terraform init 
to initialize a working directory with configuration files

    $ terraform init -backend-config ./live/preprod/eu-west-1/application/dev/backend.conf source
    
#### Step6: Run Terraform plan 
to verify the resources created by this execution

    $ terraform plan -var-file ./live/preprod/eu-west-1/application/dev/base.tfvars source

#### Step7: Finally, Terraform apply 
to create resources

    $ terraform apply -var-file ./live/preprod/eu-west-1/application/dev/base.tfvars source

### Configure kubectl and test cluster
EKS Cluster details can be extracted from terraform output or from AWS Console to get the name of cluster. This following command used to update the `kubeconfig` in your local machine where you run kubectl commands to interact with your EKS Cluster.

#### Step8: Run update-kubeconfig command. 

`~/.kube/config` file gets updated with cluster details and certificate from the below command 

    $ aws eks --region eu-west-1 update-kubeconfig --name <cluster-name>
        
#### Step9: List all the worker nodes by running the command below

    $ kubectl get nodes

#### Step10: List all the pods running in kube-system namespace

    $ kubectl get pods -n kube-system

## Deploying example templates
`example` folder contains multiple cluster templates with pre-populated `.tfvars` which can be used as a quick start. Reuse the templates from `examples` and follow the above Deployment steps as mentioned above.

# EKS Addons update
Amazon EKS doesn't modify any of your Kubernetes add-ons when you update a cluster to newer versions. 
It's important to upgrade EKS Addons Amazon VPC CNI plug-in, DNS (CoreDNS) and KubeProxy for each EKS release.

This [README](eks_cluster_addons_upgrade/README.md) guides you to update the EKS addons for newer versions that matches with your EKS cluster version

Updating a EKS cluster instructions can be found in [AWS documentation](https://docs.aws.amazon.com/eks/latest/userguide/update-cluster.html).

# Important note
This module tested only with **Kubernetes v1.19 version**. Helm Charts addon modules aligned with k8s v1.19. If you are looking to use this code to deploy different versions of Kubernetes then ensure Helm charts and docker images aligned with k8s version.

The `kubernetes_version="1.19"` is the required variable in `base.tfvars`. Kubernetes is evolving a lot, and each major version includes new features, fixes, or changes. 

Always check [Kubernetes Release Notes](https://kubernetes.io/docs/setup/release/notes/) before updating the major version. You also need to ensure your applications and Helm addons updated, 
or workloads could fail after the upgrade is complete. For action, you may need to take before upgrading, see the steps in the EKS documentation.

# Notes:
If you are using an existing VPC then you may need to ensure that the following tags added to the VPC and subnet resources

Add Tags to VPC

    Key = kubernetes.io/cluster/${local.cluster_name} Value = Shared

Add Tags to Public Subnets tagging requirement
    
      public_subnet_tags = {
        "kubernetes.io/cluster/${local.cluster_name}" = "shared"
        "kubernetes.io/role/elb"                      = "1"
      }

Add Tags to Private Subnets tagging requirement

      private_subnet_tags = {
        "kubernetes.io/cluster/${local.cluster_name}" = "shared"
        "kubernetes.io/role/internal-elb"             = "1"
      }
        
For fully Private EKS clusters requires the following VPC endpoints to be created to communicate with AWS services. This module will create these endpoints if you choose to create VPC. If you are using an existing VPC then you may need to ensure these endpoints are created.

    com.amazonaws.region.ec2
    com.amazonaws.region.ecr.api
    com.amazonaws.region.ecr.dkr
    com.amazonaws.region.s3                         – For pulling container images
    com.amazonaws.region.logs                       – For CloudWatch Logs
    com.amazonaws.region.sts                        – If using AWS Fargate or IAM roles for service accounts
    com.amazonaws.region.elasticloadbalancing       – If using Application Load Balancers
    com.amazonaws.region.autoscaling                – If using Cluster Autoscaler


# Author
Created by [Vara Bonthu](https://github.com/vara-bonthu). Maintained by [Ulaganathan N](https://github.com/UlaganathanNamachivayam), [Jomcy Pappachen](https://github.com/jomcy-amzn)

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

