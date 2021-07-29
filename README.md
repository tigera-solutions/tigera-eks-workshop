# Calico workshop on EKS

<img src="img/calico-on-eks.png" alt="Calico on EKS" width="30%"/>

## Workshop objectives

The intent of this workshop is to educate any person working with EKS cluster in one way or another about Calico features and how to use them. While there are many capabilities that Calico provides, this workshop focuses on a subset of those that are used most often by different types of technical users.

## Use cases

In this workshop we are going to focus on these main use cases:

- **East-West security**, leveraging zero-trust security approach.
- **Egress access controls**, using DNS policy to access external resources by their fully qualified domain names (FQDN).
- **Host micro-segmentation**, leveraging Calico policies to protect host ports and host based services.
- **Observability**, exploring various logs and application level metrics collected by Calico.
- **Compliance**, providing proof of security compliance.
- **Security alerts**, configuring alerts to notify security and operations teams of any security incidents or anomalous behaviors.
- **Dynamic packet capture**, capturing full packet payload on demand for further forensic analysis.

## Join the Slack Channel

[Calico User Group Slack](https://slack.projectcalico.org/) is a great resource to ask any questions about Calico. If you are not a part of this Slack group yet, we highly recommend [joining it](https://slack.projectcalico.org/) to participate in discussions or ask questions. For example, you can ask questions specific to EKS and other managed Kubernetes services in the `#eks-aks-gke-iks` channel.

## Workshop prerequisites

>It is recommended to use your personal AWS account which would have full access to AWS resources. If using a corporate AWS account for the workshop, make sure to check with account administrator to provide you with sufficient permissions to create and manage EKS clusters and Load Balancer resources.

- [Calico Cloud trial account](https://www.tigera.io/tigera-products/calico-cloud/)
  - for instructor-led workshop use instructions in the email you receive to request a Calico Trial account
  - for self-paced workshop follow the [link to register](https://www.tigera.io/tigera-products/calico-cloud/) for a Calico Trial account
- AWS account and credentials to manage AWS resources
- Terminal or Command Line console to work with AWS resources and EKS cluster
  - most common environments are Cloud9, Mac OS, Linux, Windows WSL2
- `Git`
- `netcat`

>This workshop has been designed to use [AWS Cloud9](https://docs.aws.amazon.com/cloud9/latest/user-guide/tutorial.html) instance as a workspace environment. If you're familiar with the tools listed in prerequisites section, feel free to use a workspace environment you are most comfortable with.

## Modules

- [Module 1: Setting up workspace environment](./modules/setting-up-work-environment.md)
- [Module 2: Creating EKS cluster](modules/creating-eks-cluster.md)
- [Module 3: Joining EKS cluster to Calico Cloud](modules/joining-eks-to-calico-cloud.md)
- [Module 4: Configuring demo applications](modules/configuring-demo-apps.md)
- [Module 5: Using security controls](modules/using-security-controls.md)
- [Module 6: Using egress access controls](modules/using-egress-access-controls.md)
- [Module 7: Securing EKS hosts](modules/securing-heps.md)
- [Module 8: Layer 7 Logging](modules/layer7-logging.md)
- [Module 9: Using observability tools](modules/using-observability-tools.md)
- [Module 10: Using compliance reports](modules/using-compliance-reports.md)
- [Module 11: Using alerts](modules/using-alerts.md)
- [Module 12: Dynamic packet capture](modules/dynamic-packet-capture.md)
- [Module 13: Anomaly Detection](modules/anomaly-detection.md)

## Cleanup

1. Delete application stack to clean up any `loadbalancer` services.

    ```bash
    kubectl delete -f demo/dev/app.manifests.yaml
    kubectl delete -f https://raw.githubusercontent.com/GoogleCloudPlatform/microservices-demo/master/release/kubernetes-manifests.yaml
    ```

2. Delete EKS cluster.

    ```bash
    eksctl delete cluster --name tigera-workshop
    ```

3. Delete EC2 Key Pair.

    ```bash
    export KEYPAIR_NAME='<set_keypair_name>'
    aws ec2 delete-key-pair --key-name $KEYPAIR_NAME
    ```

4. Delete Cloud9 instance.

    Navigate to `AWS Console` > `Services` > `Cloud9` and remove your workspace environment, e.g. `tigera-workshop`.

5. Delete IAM role created for this workshop.

    ```bash
    # use your local shell to set AWS credentials
    export AWS_ACCESS_KEY_ID="<your_accesskey_id>"
    export AWS_SECRET_ACCESS_KEY="<your_secretkey>"

    # delete IAM role
    IAM_ROLE='tigera-workshop-admin'
    ADMIN_POLICY_ARN=$(aws iam list-policies --query 'Policies[?PolicyName==`AdministratorAccess`].Arn' --output text)
    aws iam detach-role-policy --role-name $IAM_ROLE --policy-arn $ADMIN_POLICY_ARN
    # if this command fails, you can remove the role via AWS Console once you delete the Cloud9 instance
    aws iam delete-instance-profile --instance-profile-name $IAM_ROLE
    aws iam delete-role --role-name $IAM_ROLE
    ```
