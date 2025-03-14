# Cluster Autoscaler Helm Chart

    
###### Instructions to upload Cluster Autoscaler Docker image to AWS ECR

Step1: Get the latest docker image from this link
        
        https://github.com/kubernetes/autoscaler
        
Step2: Download the docker image to your local Mac/Laptop. The image address listed on the [releases page](https://github.com/kubernetes/autoscaler/releases)
       Please note that image version should match with Kubernetes version.
        
        $ docker pull k8s.gcr.io/autoscaling/cluster-autoscaler:v1.19.1
        
Step3: Retrieve an authentication token and authenticate your Docker client to your registry. Use the AWS CLI:
        
        $ aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin <account id>.dkr.ecr.eu-west-1.amazonaws.com
        
Step4: Create an ECR repo for Metrics Server if you don't have one 
    
        $ aws ecr create-repository --repository-name k8s.gcr.io/autoscaling/cluster-autoscaler --image-scanning-configuration scanOnPush=true 
              
Step5: After the build completes, tag your image so, you can push the image to this repository:
        
        $ docker tag k8s.gcr.io/autoscaling/cluster-autoscaler:v1.19.1 <account id>.dkr.ecr.eu-west-1.amazonaws.com/k8s.gcr.io/autoscaling/cluster-autoscaler:v1.19.1
        
Step6: Run the following command to push this image to your newly created AWS repository:
        
        $ docker push <account id>.dkr.ecr.eu-west-1.amazonaws.com/k8s.gcr.io/autoscaling/cluster-autoscaler:v1.19.1

### Instructions to download Helm Charts

#### Helm Chart
    
    hthttps://artifacthub.io/packages/helm/cluster-autoscaler/cluster-autoscaler
    
    https://github.com/kubernetes/autoscaler/tree/cluster-autoscaler-chart-9.9.2/cluster-autoscaler
    

#### Helm Repo Maintainers

    helm repo add autoscaler https://kubernetes.github.io/autoscaler

#### Helm Chart values.yaml

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

No requirements.

## Providers

| Name | Version |
|------|---------|
| <a name="provider_helm"></a> [helm](#provider\_helm) | n/a |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [helm_release.autoscaler](https://registry.terraform.io/providers/hashicorp/helm/latest/docs/resources/release) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_eks_cluster_id"></a> [eks\_cluster\_id](#input\_eks\_cluster\_id) | EKS\_Cluster\_ID | `any` | n/a | yes |
| <a name="input_image_repo_name"></a> [image\_repo\_name](#input\_image\_repo\_name) | n/a | `string` | `"k8s.gcr.io/autoscaling/cluster-autoscaler"` | no |
| <a name="input_image_repo_url"></a> [image\_repo\_url](#input\_image\_repo\_url) | n/a | `any` | n/a | yes |
| <a name="input_image_tag"></a> [image\_tag](#input\_image\_tag) | n/a | `string` | `"v1.19.1"` | no |
| <a name="input_public_docker_repo"></a> [public\_docker\_repo](#input\_public\_docker\_repo) | n/a | `any` | n/a | yes |

## Outputs

No outputs.
<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->


```yaml
    ## Ref: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
    # affinity -- Affinity for pod assignment
    affinity: {}
    
    autoDiscovery:
      # cloudProviders `aws`, `gce` and `magnum` are supported by auto-discovery at this time
      # AWS: Set tags as described in https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/aws/README.md#auto-discovery-setup
    
      # autoDiscovery.clusterName -- Enable autodiscovery for `cloudProvider=aws`, for groups matching `autoDiscovery.tags`.
      # Enable autodiscovery for `cloudProvider=gce`, but no MIG tagging required.
      # Enable autodiscovery for `cloudProvider=magnum`, for groups matching `autoDiscovery.roles`.
      clusterName:  # cluster.local
    
      # autoDiscovery.tags -- ASG tags to match, run through `tpl`.
      tags:
      - k8s.io/cluster-autoscaler/enabled
      - k8s.io/cluster-autoscaler/{{ .Values.autoDiscovery.clusterName }}
      # - kubernetes.io/cluster/{{ .Values.autoDiscovery.clusterName }}
    
      # autoDiscovery.roles -- Magnum node group roles to match.
      roles:
      - worker
    
    # autoscalingGroups -- For AWS, Azure AKS or Magnum. At least one element is required if not using `autoDiscovery`. For example:
    # <pre>
    # - name: asg1<br />
    #   maxSize: 2<br />
    #   minSize: 1
    # </pre>
    autoscalingGroups: []
    # - name: asg1
    #   maxSize: 2
    #   minSize: 1
    # - name: asg2
    #   maxSize: 2
    #   minSize: 1
    
    # autoscalingGroupsnamePrefix -- For GCE. At least one element is required if not using `autoDiscovery`. For example:
    # <pre>
    # - name: ig01<br />
    #   maxSize: 10<br />
    #   minSize: 0
    # </pre>
    autoscalingGroupsnamePrefix: []
    # - name: ig01
    #   maxSize: 10
    #   minSize: 0
    # - name: ig02
    #   maxSize: 10
    #   minSize: 0
    
    # awsAccessKeyID -- AWS access key ID ([if AWS user keys used](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/aws/README.md#using-aws-credentials))
    awsAccessKeyID: ""
    
    # awsRegion -- AWS region (required if `cloudProvider=aws`)
    awsRegion: us-east-1
    
    # awsSecretAccessKey -- AWS access secret key ([if AWS user keys used](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/aws/README.md#using-aws-credentials))
    awsSecretAccessKey: ""
    
    # azureClientID -- Service Principal ClientID with contributor permission to Cluster and Node ResourceGroup.
    # Required if `cloudProvider=azure`
    azureClientID: ""
    
    # azureClientSecret -- Service Principal ClientSecret with contributor permission to Cluster and Node ResourceGroup.
    # Required if `cloudProvider=azure`
    azureClientSecret: ""
    
    # azureResourceGroup -- Azure resource group that the cluster is located.
    # Required if `cloudProvider=azure`
    azureResourceGroup: ""
    
    # azureSubscriptionID -- Azure subscription where the resources are located.
    # Required if `cloudProvider=azure`
    azureSubscriptionID: ""
    
    # azureTenantID -- Azure tenant where the resources are located.
    # Required if `cloudProvider=azure`
    azureTenantID: ""
    
    # azureVMType -- Azure VM type.
    azureVMType: "AKS"
    
    # azureClusterName -- Azure AKS cluster name.
    # Required if `cloudProvider=azure`
    azureClusterName: ""
    
    # azureNodeResourceGroup -- Azure resource group where the cluster's nodes are located, typically set as `MC_<cluster-resource-group-name>_<cluster-name>_<location>`.
    # Required if `cloudProvider=azure`
    azureNodeResourceGroup: ""
    
    # azureUseManagedIdentityExtension -- Whether to use Azure's managed identity extension for credentials. If using MSI, ensure subscription ID and resource group are set.
    azureUseManagedIdentityExtension: false
    
    # magnumClusterName -- Cluster name or ID in Magnum.
    # Required if `cloudProvider=magnum` and not setting `autoDiscovery.clusterName`.
    magnumClusterName: ""
    
    # magnumCABundlePath -- Path to the host's CA bundle, from `ca-file` in the cloud-config file.
    magnumCABundlePath: "/etc/kubernetes/ca-bundle.crt"
    
    # cloudConfigPath -- Configuration file for cloud provider.
    cloudConfigPath: /etc/gce.conf
    
    # cloudProvider -- The cloud provider where the autoscaler runs.
    # Currently only `gce`, `aws`, `azure` and `magnum` are supported.
    # `aws` supported for AWS. `gce` for GCE. `azure` for Azure AKS.
    # `magnum` for OpenStack Magnum.
    cloudProvider: aws
    
    # containerSecurityContext -- [Security context for container](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)
    containerSecurityContext: {}
      # capabilities:
      #   drop:
      #   - ALL
    
    # dnsPolicy -- Defaults to `ClusterFirst`. Valid values are:
    # `ClusterFirstWithHostNet`, `ClusterFirst`, `Default` or `None`.
    # If autoscaler does not depend on cluster DNS, recommended to set this to `Default`.
    dnsPolicy: ClusterFirst
    
    ## Priorities Expander
    # expanderPriorities -- The expanderPriorities is used if `extraArgs.expander` is set to `priority` and expanderPriorities is also set with the priorities.
    # If `extraArgs.expander` is set to `priority`, then expanderPriorities is used to define cluster-autoscaler-priority-expander priorities.
    # See: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/expander/priority/readme.md
    expanderPriorities: {}
    
    # extraArgs -- Additional container arguments.
    # Refer to https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca for the full list of cluster autoscaler
    # parameters and their default values.
    extraArgs:
      logtostderr: true
      stderrthreshold: info
      v: 4
      # write-status-configmap: true
      # status-config-map-name: cluster-autoscaler-status
      # leader-elect: true
      # skip-nodes-with-local-storage: true
      # expander: random
      # scale-down-enabled: true
      # balance-similar-node-groups: true
      # min-replica-count: 0
      # scale-down-utilization-threshold: 0.5
      # scale-down-non-empty-candidates-count: 30
      # max-node-provision-time: 15m0s
      # scan-interval: 10s
      # scale-down-delay-after-add: 10m
      # scale-down-delay-after-delete: 0s
      # scale-down-delay-after-failure: 3m
      # scale-down-unneeded-time: 10m
      # skip-nodes-with-system-pods: true
    
    # extraEnv -- Additional container environment variables.
    extraEnv: {}
    
    # extraEnvConfigMaps -- Additional container environment variables from ConfigMaps.
    extraEnvConfigMaps: {}
    
    # extraEnvSecrets -- Additional container environment variables from Secrets.
    extraEnvSecrets: {}
    
    # envFromConfigMap -- ConfigMap name to use as envFrom.
    envFromConfigMap: ""
    
    # envFromSecret -- Secret name to use as envFrom.
    envFromSecret: ""
    
    # extraVolumeSecrets -- Additional volumes to mount from Secrets.
    extraVolumeSecrets: {}
      # autoscaler-vol:
      #   mountPath: /data/autoscaler/
      # custom-vol:
      #   name: custom-secret
      #   mountPath: /data/custom/
      #   items:
      #     - key: subkey
      #       path: mypath
    
    # extraVolumes -- Additional volumes.
    extraVolumes: []
      # - name: ssl-certs
      #   hostPath:
      #     path: /etc/ssl/certs/ca-bundle.crt
    
    # extraVolumeMounts -- Additional volumes to mount.
    extraVolumeMounts: []
      # - name: ssl-certs
      #   mountPath: /etc/ssl/certs/ca-certificates.crt
      #   readOnly: true
    
    # fullnameOverride -- String to fully override `cluster-autoscaler.fullname` template.
    fullnameOverride: ""
    
    image:
      # image.repository -- Image repository
      repository: k8s.gcr.io/autoscaling/cluster-autoscaler
      # image.tag -- Image tag
      tag: v1.19.1
      # image.pullPolicy -- Image pull policy
      pullPolicy: IfNotPresent
      ## Optionally specify an array of imagePullSecrets.
      ## Secrets must be manually created in the namespace.
      ## ref: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/
      ##
      # image.pullSecrets -- Image pull secrets
      pullSecrets: []
      # - myRegistrKeySecretName
    
    # kubeTargetVersionOverride -- Allow overriding the `.Capabilities.KubeVersion.GitVersion` check. Useful for `helm template` commands.
    kubeTargetVersionOverride: ""
    
    # nameOverride -- String to partially override `cluster-autoscaler.fullname` template (will maintain the release name)
    nameOverride: ""
    
    # nodeSelector -- Node labels for pod assignment. Ref: https://kubernetes.io/docs/user-guide/node-selection/.
    nodeSelector: {}
    
    # podAnnotations -- Annotations to add to each pod.
    podAnnotations: {}
    
    # podDisruptionBudget -- Pod disruption budget.
    podDisruptionBudget:
      maxUnavailable: 1
      # minAvailable: 2
    
    # podLabels -- Labels to add to each pod.
    podLabels: {}
    
    # additionalLabels -- Labels to add to each object of the chart.
    additionalLabels: {}
    
    # priorityClassName -- priorityClassName
    priorityClassName: ""
    
    rbac:
      # rbac.create -- If `true`, create and use RBAC resources.
      create: true
      # rbac.pspEnabled -- If `true`, creates and uses RBAC resources required in the cluster with [Pod Security Policies](https://kubernetes.io/docs/concepts/policy/pod-security-policy/) enabled.
      # Must be used with `rbac.create` set to `true`.
      pspEnabled: false
      serviceAccount:
        # rbac.serviceAccount.annotations -- Additional Service Account annotations.
        annotations: {}
        # rbac.serviceAccount.create -- If `true` and `rbac.create` is also true, a Service Account will be created.
        create: true
        # rbac.serviceAccount.name -- The name of the ServiceAccount to use. If not set and create is `true`, a name is generated using the fullname template.
        name: ""
        # rbac.serviceAccount.automountServiceAccountToken -- Automount API credentials for a Service Account.
        automountServiceAccountToken: true
    
    # replicaCount -- Desired number of pods
    replicaCount: 1
    
    # resources -- Pod resource requests and limits.
    resources: {}
      # limits:
      #   cpu: 100m
      #   memory: 300Mi
      # requests:
      #   cpu: 100m
      #   memory: 300Mi
    
    # securityContext -- [Security context for pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)
    securityContext: {}
      # runAsNonRoot: true
      # runAsUser: 1001
      # runAsGroup: 1001
    
    service:
      # service.annotations -- Annotations to add to service
      annotations: {}
      # service.labels -- Labels to add to service
      labels: {}
      # service.externalIPs -- List of IP addresses at which the service is available. Ref: https://kubernetes.io/docs/user-guide/services/#external-ips.
      externalIPs: []
    
      # service.loadBalancerIP -- IP address to assign to load balancer (if supported).
      loadBalancerIP: ""
      # service.loadBalancerSourceRanges -- List of IP CIDRs allowed access to load balancer (if supported).
      loadBalancerSourceRanges: []
      # service.servicePort -- Service port to expose.
      servicePort: 8085
      # service.portName -- Name for service port.
      portName: http
      # service.type -- Type of service to create.
      type: ClusterIP
    
    ## Are you using Prometheus Operator?
    serviceMonitor:
      # serviceMonitor.enabled -- If true, creates a Prometheus Operator ServiceMonitor.
      enabled: false
      # serviceMonitor.interval -- Interval that Prometheus scrapes Cluster Autoscaler metrics.
      interval: 10s
      # serviceMonitor.namespace -- Namespace which Prometheus is running in.
      namespace: monitoring
      ## [Prometheus Selector Label](https://github.com/helm/charts/tree/master/stable/prometheus-operator#prometheus-operator-1)
      ## [Kube Prometheus Selector Label](https://github.com/helm/charts/tree/master/stable/prometheus-operator#exporters)
      # serviceMonitor.selector -- Default to kube-prometheus install (CoreOS recommended), but should be set according to Prometheus install.
      selector:
        release: prometheus-operator
      # serviceMonitor.path -- The path to scrape for metrics; autoscaler exposes `/metrics` (this is standard)
      path: /metrics
    
    ## Custom PrometheusRule to be defined
    ## The value is evaluated as a template, so, for example, the value can depend on .Release or .Chart
    ## ref: https://github.com/coreos/prometheus-operator#customresourcedefinitions
    prometheusRule:
      # prometheusRule.enabled -- If true, creates a Prometheus Operator PrometheusRule.
      enabled: false
      # prometheusRule.additionalLabels -- Additional labels to be set in metadata.
      additionalLabels: {}
      # prometheusRule.namespace -- Namespace which Prometheus is running in.
      namespace: monitoring
      # prometheusRule.interval -- How often rules in the group are evaluated (falls back to `global.evaluation_interval` if not set).
      interval: null
      # prometheusRule.rules -- Rules spec template (see https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/api.md#rule).
      rules: []
    
    # tolerations -- List of node taints to tolerate (requires Kubernetes >= 1.6).
    tolerations: []
    
    # updateStrategy -- [Deployment update strategy](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)
    updateStrategy: {}
      # rollingUpdate:
      #   maxSurge: 1
      #   maxUnavailable: 0
      # type: RollingUpdate

```