# aws-for-fluent-bit Helm Chart

###### Instructions to upload aws-for-fluent-bit Docker image to AWS ECR

Step1: Get the latest docker image from this link
        
        https://github.com/aws/aws-for-fluent-bit
        
Step2: Download the docker image to your local Mac/Laptop
        
        $ docker pull amazon/aws-for-fluent-bit:2.12.0
        
Step3: Retrieve an authentication token and authenticate your Docker client to your registry. Use the AWS CLI:
        
        $ aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin <account id>.dkr.ecr.eu-west-1.amazonaws.com
        
Step4: Create an ECR repo for Metrics Server if you don't have one 
    
        $ aws ecr create-repository --repository-name amazon/aws-for-fluent-bit --image-scanning-configuration scanOnPush=true 
              
Step5: After the build completes, tag your image so, you can push the image to this repository:
        
        $ docker tag amazon/aws-for-fluent-bit:2.12.0 <accountid>.dkr.ecr.eu-west-1.amazonaws.com/amazon/aws-for-fluent-bit:2.12.0

Step6: Run the following command to push this image to your newly created AWS repository:
        
        $ docker push <accountid>.dkr.ecr.eu-west-1.amazonaws.com/amazon/aws-for-fluent-bit:2.12.0

### Instructions to download Helm Charts

Helm Chart
    
    https://artifacthub.io/packages/helm/aws/aws-for-fluent-bit

Helm Repo Maintainers

    https://github.com/aws/eks-charts


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
| [aws_cloudwatch_log_group.eks-worker-logs](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/cloudwatch_log_group) | resource |
| [helm_release.aws-for-fluent-bit](https://registry.terraform.io/providers/hashicorp/helm/latest/docs/resources/release) | resource |
| [kubernetes_namespace.logging](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs/resources/namespace) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_cluster_id"></a> [cluster\_id](#input\_cluster\_id) | n/a | `any` | n/a | yes |
| <a name="input_ekslog_retention_in_days"></a> [ekslog\_retention\_in\_days](#input\_ekslog\_retention\_in\_days) | n/a | `any` | n/a | yes |
| <a name="input_image_repo_name"></a> [image\_repo\_name](#input\_image\_repo\_name) | n/a | `string` | `"amazon/aws-for-fluent-bit"` | no |
| <a name="input_image_repo_url"></a> [image\_repo\_url](#input\_image\_repo\_url) | n/a | `any` | n/a | yes |
| <a name="input_image_tag"></a> [image\_tag](#input\_image\_tag) | n/a | `string` | `"2.12.0"` | no |
| <a name="input_public_docker_repo"></a> [public\_docker\_repo](#input\_public\_docker\_repo) | n/a | `any` | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_cw_loggroup_arn"></a> [cw\_loggroup\_arn](#output\_cw\_loggroup\_arn) | EKS Cloudwatch group arn |
| <a name="output_cw_loggroup_name"></a> [cw\_loggroup\_name](#output\_cw\_loggroup\_name) | EKS Cloudwatch group Name |
<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

