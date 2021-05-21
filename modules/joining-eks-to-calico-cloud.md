# Module 3: Joining EKS cluster to Calico Cloud

**Goal:** Join EKS cluster to Calico Cloud management plane.

>In order to complete this module, you must have [Calico Cloud trial account](https://www.tigera.io/tigera-products/calico-cloud/).

## Steps

1. Join EKS cluster to Calico Cloud management plane.

    Use Calico Cloud install script provided in the welcome email for Calico Cloud trial account.

    ```bash
    # script should look similar to this
    curl https://installer.calicocloud.io/xxxxxx_yyyyyyy-saay-management_install.sh | bash
    ```

    Joining the cluster to Calico Cloud can take a few minutes. Wait for the installation script to finish before you proceed to the next step.

2. Configure log aggregation and flush intervals.

    ```bash
    kubectl patch felixconfiguration.p default -p '{"spec":{"flowLogsFlushInterval":"10s"}}'
    kubectl patch felixconfiguration.p default -p '{"spec":{"dnsLogsFlushInterval":"10s"}}'
    kubectl patch felixconfiguration.p default -p '{"spec":{"flowLogsFileAggregationKindForAllowed":1}}'
    ```

3. Configure Felix for log data collection.

    ```bash
    kubectl patch felixconfiguration default --type='merge' -p '{"spec":{"policySyncPathPrefix":"/var/run/nodeagent","l7LogsFileEnabled":true}}'
    ```

4. Create admin user and retrieve token.

    In order to login into Enterprise Manager UI we need to configure user and **use the users's token to login** to access the Enterprise Manager UI.

    >For more authentication options refer to [official auth quickstart documentation](https://docs.tigera.io/getting-started/cnx/authentication-quickstart) or [auth using external identity provider](https://docs.tigera.io/getting-started/cnx/configure-identity-provider).

    ```bash
    # create service account and RBAC for it
    kubectl create sa jane
    kubectl create clusterrolebinding jane-access --clusterrole tigera-network-admin --serviceaccount default:jane

    # get service account token
    kubectl get secret $(kubectl get serviceaccount jane -o jsonpath='{range .secrets[*]}{.name}{"\n"}{end}' | grep token) -o go-template='{{.data.token | base64decode}}'
    ```

[Next -> Module 4](../modules/configuring-demo-apps.md)
