# Module 5: Enable application layer monitoring

**Goal:** Leverage L7 logs to get insight into application layer communications.

For more details on L7 logs, refer to the [official documentation](https://docs.tigera.io/v3.11/visibility/elastic/l7/configure).

>This module is applicable to Calico Cloud or Calico Enterprise version v3.10+. If your Calico version is lower than v3.10.0, then skip this task. You can verify Calico version, by running command:  
`kubectl get clusterinformation default -ojsonpath='{.spec.cnxVersion}'`

>L7 collector is based on the Envoy proxy which gets automatically deployed via `ApplicationLayer` resource configuration. For more details, see [Configure L7 logs](https://docs.tigera.io/visibility/elastic/l7/configure) documentation page.

## Steps

1. Deploy `ApplicationLayer` resource.

    a. Deploy `ApplicationLayer` resource.

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: operator.tigera.io/v1
    kind: ApplicationLayer
    metadata:
      name: tigera-secure
    spec:
      logCollection:
        collectLogs: Enabled
        logIntervalSeconds: 5
        logRequestsPerInterval: -1
    EOF
    ```    
   >This creates `l7-log-collector` daemonset in the `calico-system` namespace which contains `enovy-proxy` pod for application log collection and security.

    b. Enable L7 logs for the application service.

    To opt a service into L7 log collection, you need to annotate the service with `projectcalico.org/l7-logging=true` annotation.

    ```bash
    # enable L7 logs for a few services of boutiqueshop app
    kubectl annotate svc frontend projectcalico.org/l7-logging=true
    kubectl annotate svc checkoutservice projectcalico.org/l7-logging=true
    ```

In module 9 you will review Calico's observability tools and can see application layer information for the `frontend` and `checkout` service in the Service Graph tool.

[Next -> Module 6](../modules/using-security-controls.md)
