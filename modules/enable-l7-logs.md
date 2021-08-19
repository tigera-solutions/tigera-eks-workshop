# Module 5: Enable application layer monitoring

**Goal:** Leverage L7 logs to get insight into application layer communications.

For more details on L7 logs, refer to the [official documentation](https://docs.tigera.io/v3.9/visibility/elastic/l7/configure#step-2-enable-l7-log-collection).

>This module is applicable to Calico Cloud or Calico Enterprise version v3.9+. If your Calico version is lower than v3.9.0, then skip this module. You can verify Calico version, by running command:  
`kubectl get clusterinformation default -ojsonpath='{.spec.cnxVersion}'`.

## Steps

1. Enable L7 logs.

    >L7 collector is based on the Envoy proxy and deployed as a DaemonSet resource.

    a. Enable the Policy Sync API in `Felix`.

    >[Felix](https://docs.tigera.io/reference/architecture/overview#felix) is one of Calico components that is responsible for configuring routes, ACLs, and anything else required on the host to provide desired connectivity for the endpoints on that host.

    ```bash
    kubectl patch felixconfiguration default --type='merge' -p '{"spec":{"policySyncPathPrefix":"/var/run/nodeagent"}}'
    ```

    b. Deploy Envoy configuration.

    ```bash
    # download Envoy config
    curl https://docs.tigera.io/manifests/l7/daemonset/envoy-config.yaml -O
    
    # deploy Envoy config
    kubectl create configmap envoy-config -n calico-system --from-file=envoy-config.yaml
    ```

    c. Deploy L7 collector component.

    ```bash
    # deploy L7 collector
    kubectl apply -f https://docs.tigera.io/manifests/l7/daemonset/l7-collector-daemonset.yaml

    # enable L7 log collection daemonset mode in Felix
    kubectl patch felixconfiguration default --type='merge' -p '{"spec":{"tproxyMode":"Enabled"}}'
    ```

2. Enable L7 logs for the application service.

    To opt a service into L7 log collection, you need to annotate the service with `projectcalico.org/l7-logging=true` annotation.

    ```bash
    # enable L7 logs for frontend service
    kubectl annotate svc frontend projectcalico.org/l7-logging=true
    ```

In module 9 you will review Calico's observability tools and can see application layer information for the `frontend` service in the Service Graph tool.

[Next -> Module 6](../modules/using-security-controls.md)
