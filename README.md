# Calico workshop on EKS

<img src="img/calico-on-eks.png" alt="Calico on EKS" width="25%"/>

## Workshop objectives

The intent of this workshop is to educate any person working with EKS cluster in one way or another about Calico features and how to use them. While there are many capabilities that Calico provides, this workshop focuses on a subset of those that are used most often by different types of technical users.

## Use cases

In this workshop we are going to focus on these main use cases:

- **East-West security**, leveraging zero-trust security approach.
- **Egress access controls**, using DNS policy to access external resources by their fully qualified domain names (FQDN).
- **Host micro-segmentation**, leveraging Calico policies to protect host ports and host based services.
- **Observability**, exploring various logs and application level metrics collected by Calico.
- **Compliance**, providing proof of security compliance.

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
- Git

>This workshop has been designed to use [AWS Cloud9](https://docs.aws.amazon.com/cloud9/latest/user-guide/tutorial.html) instance as a work environment. If you're familiar with the tools listed in prerequisites section, feel free to use a work environment of your choosing.

## Modules

- [Module 1: Setting up workspace environment](./modules/setting-up-work-environment.md)
- [Module 2: Creating EKS cluster](modules/creating-eks-cluster.md)
- [Module 3: Joining EKS cluster to Calico Cloud](modules/joining-eks-to-calico-cloud.md)
- [Module 4: Configuring demo applications](modules/configuring-demo-apps.md)
- [Module 5: Securing East-West traffic](modules/securing-east-west-traffic.md)
- [Module 6: Using egress access controls](modules/)
- [Module 7: Securing EKS hosts](modules/)
- [Module 8: Using observability tools](modules/)
- [Module 9: Using compliance reports](modules/)

## Cleanup

1. Delete any `loadbalancer` service deployed for the EKS cluster

    ```bash
    kubectl delete -f manager-lb.yaml
    ```

2. Delete EKS cluster

    ```bash
    # eksctl delete cluster --name cluster-name
    ```

3. Delete Cloud9 instance

Navigate to AWS Console -> Services -> Cloud9 and remove your work environment, e.g. `tigera-workshop`.
