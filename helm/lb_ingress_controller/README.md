# LB Ingress Controller Deployment Guide

# Introduction

AWS Load Balancer Controller is a controller to help manage Elastic Load Balancers for a Kubernetes cluster.

* It satisfies Kubernetes Ingress resources by provisioning Application Load Balancers.
* It satisfies Kubernetes Service resources by provisioning Network Load Balancers.
 
# Helm Chart

### Instructions to use Helm Charts

Add Helm repo for LB Ingress Controller

    helm repo add aws-load-balancer-controller https://aws.github.io/eks-charts

    https://github.com/aws/eks-charts/blob/master/stable/aws-load-balancer-controller/values.yaml

    https://artifacthub.io/packages/helm/aws/aws-load-balancer-controller
    

# Docker Image for LB ingress controller

###### Instructions to upload LB ingress controller Docker image to AWS ECR

Step1: Get the latest docker image from this link
        
        https://github.com/aws/eks-charts/blob/master/stable/aws-load-balancer-controller/values.yaml
        
Step2: Download the docker image to your local Mac/Laptop

        $ aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 602401143452.dkr.ecr.us-west-2.amazonaws.com
        
        $ docker pull 602401143452.dkr.ecr.us-west-2.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
        
Step3: Retrieve an authentication token and authenticate your Docker client to your registry. Use the AWS CLI:
        
        $ aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin <account id>.dkr.ecr.eu-west-1.amazonaws.com
        
Step4: Create an ECR repo for LB ingress controller if you don't have one 
    
        $ aws ecr create-repository \
              --repository-name amazon/aws-load-balancer-controller \
              --image-scanning-configuration scanOnPush=true 
              
Step5: After the build completes, tag your image so, you can push the image to this repository:
        
        $ docker tag amazon/aws-load-balancer-controller:v2.1.3 <accountid>.dkr.ecr.eu-west-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
        
Step6: Run the following command to push this image to your newly created AWS repository:
        
        $ docker push <accountid>.dkr.ecr.eu-west-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3


#### AWS Service annotations for LB Ingress Controller
Here is the link to get the AWS ELB [service annotations](https://kubernetes-sigs.github.io/aws-load-balancer-controller/latest/guide/service/annotations/) for LB Ingress controller


<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

No requirements.

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | n/a |
| <a name="provider_helm"></a> [helm](#provider\_helm) | n/a |
| <a name="provider_kubernetes"></a> [kubernetes](#provider\_kubernetes) | n/a |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [aws_iam_policy.eks-lb-controller-policy](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_policy) | resource |
| [aws_iam_role.eks-lb-controller-role](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_iam_role_policy_attachment.eks-role-policy-attachement](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy_attachment) | resource |
| [helm_release.lb-ingress](https://registry.terraform.io/providers/hashicorp/helm/latest/docs/resources/release) | resource |
| [helm_release.lb-ingress-crd](https://registry.terraform.io/providers/hashicorp/helm/latest/docs/resources/release) | resource |
| [kubernetes_service_account.eks-lb-controller-sa](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs/resources/service_account) | resource |
| [aws_iam_policy_document.eks-lb-controller-assume-policy](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_clusterName"></a> [clusterName](#input\_clusterName) | n/a | `any` | n/a | yes |
| <a name="input_eks_oidc_issuer_url"></a> [eks\_oidc\_issuer\_url](#input\_eks\_oidc\_issuer\_url) | n/a | `any` | n/a | yes |
| <a name="input_eks_oidc_provider_arn"></a> [eks\_oidc\_provider\_arn](#input\_eks\_oidc\_provider\_arn) | n/a | `any` | n/a | yes |
| <a name="input_image_repo_name"></a> [image\_repo\_name](#input\_image\_repo\_name) | n/a | `string` | `"amazon/aws-load-balancer-controller"` | no |
| <a name="input_image_repo_url"></a> [image\_repo\_url](#input\_image\_repo\_url) | n/a | `any` | n/a | yes |
| <a name="input_image_tag"></a> [image\_tag](#input\_image\_tag) | n/a | `string` | `"v2.1.3"` | no |
| <a name="input_public_docker_repo"></a> [public\_docker\_repo](#input\_public\_docker\_repo) | n/a | `any` | n/a | yes |
| <a name="input_replicas"></a> [replicas](#input\_replicas) | n/a | `string` | `"2"` | no |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_ingress_name"></a> [ingress\_name](#output\_ingress\_name) | n/a |
| <a name="output_ingress_namespace"></a> [ingress\_namespace](#output\_ingress\_namespace) | n/a |
<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->




