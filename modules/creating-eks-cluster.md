# Module 2: Creating EKS cluster

**Goal:** Create EKS cluster.

>This workshop uses EKS cluster with most of the default configuration settings. To create an EKS cluster and tune the default settings, consider exploring [EKS Workshop](https://www.eksworkshop.com) materials.

## Steps

1. Configure variables.

    ```bash
    export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
    export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
    EKS_VERSION="1.19"
    
    # check if AWS_REGION is configured
    test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set

    # add vars to .bash_profile
    echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
    echo "export AZS=(${AZS[@]})" | tee -a ~/.bash_profile
    aws configure set default.region ${AWS_REGION}
    aws configure get default.region

    # remove ~/.aws/credentials file if it exists
    rm -vf $HOME/.aws/credentials

    # verify that IAM role is configured correctly. IAM_ROLE was set in previous module to tigera-workshop-admin.
    aws --profile default sts get-caller-identity --query Arn | grep $IAM_ROLE -q && echo "IAM role valid" || echo "IAM role NOT valid"
    ```

2. Create EKS manifest.

    ```bash
    # create EKS manifest file
    cat > configs/tigera-workshop.yaml << EOF
    apiVersion: eksctl.io/v1alpha5
    kind: ClusterConfig

    metadata:
      name: "tigera-workshop"
      region: "${AWS_REGION}"
      version: "${EKS_VERSION}"

    availabilityZones: ["${AZS[0]}", "${AZS[1]}", "${AZS[2]}"]

    managedNodeGroups:
    - name: "nix-t3-large"
      desiredCapacity: 3
      # choose proper size for worker node instance as the node size detemines the number of pods that a node can run
      # it's limited by a max number of interfeces and private IPs per interface
      # t3.large has max 3 interfaces and allows up to 12 IPs per interface, therefore can run up to 36 pods per node
      # see: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI
      instanceType: "t3.large"
      ssh:
        enableSsm: true

    # enable all of the control plane logs:
    cloudWatch:
      clusterLogging:
        enableTypes: ["*"]
    EOF
    ```

3. Use `eksctl` to create EKS cluster.

    ```bash
    eksctl create cluster -f configs/tigera-workshop.yaml
    ```

4. View EKS cluster.

    Once cluster is created you can list it using `eksctl`.

    ```bash
    eksctl get cluster tigera-workshop
    ```

5. Test access to EKS cluster with `kubectl`

    Once the EKS cluster is provisioned with `eksctl` tool, the `kubeconfig` file would be placed into `~/.kube/config` path. The `kubectl` CLI looks for `kubeconfig` at `~/.kube/config` path or into `KUBECONFIG` env var.

    ```bash
    # verify kubeconfig file path
    ls ~/.kube/config
    # test cluster connection
    kubectl get nodes
    ```

[Next -> Module 3](../modules/joining-eks-to-calico-cloud.md)
