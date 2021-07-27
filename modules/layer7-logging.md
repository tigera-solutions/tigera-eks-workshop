# Module 5: Layer 7 Logging and Visibility

**Goal:** Enable Layer 7 visibility for Pod traffic.
---

Calico Cloud can be enabled for Layer 7 application visibility which captures the HTTP calls applications are making. Application visibility does not require a service mesh but does utilise envoy for capturing logs. For more info please review the [documentation](https://docs.tigera.io/v3.7/visibility/elastic/l7/configure).

## Steps

1. Prepare scripts for deploying envoy sidecars

    ```bash
    DOCS_LOCATION=${DOCS_LOCATION:="https://docs.tigera.io"}

     # add tigera pull secret to the namespace. We clone the existing secret from the calico-system NameSpace
    kubectl get secret tigera-pull-secret --namespace=calico-system -o yaml | \
    grep -v '^[[:space:]]*namespace:[[:space:]]*calico-system' | \
    kubectl apply --namespace=default -f -

    #Download Envoy patch
    curl ${DOCS_LOCATION}/manifests/l7/patch-envoy.yaml -O

    #Download and install Envoy Config
    curl ${DOCS_LOCATION}/manifests/l7/envoy-config.yaml -O
    ```
2. Deploy envoy containers
    ```
    kubectl create configmap envoy-config -n default --from-file=envoy-config.yaml

    #Configure Felix to collect L7
    kubectl patch felixconfiguration default --type='merge' -p '{"spec":{"policySyncPathPrefix":"/var/run/nodeagent","l7LogsFileEnabled":true}}'
    ```
3.  Patch Boutiqueshop apps

    ```bash
    kubectl patch deployment adservice --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment cartservice --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment checkoutservice --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment currencyservice --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment emailservice --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment frontend --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment loadgenerator --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment paymentservice --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment productcatalogservice --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment recommendationservice --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment redis-cart --patch "$(cat patch-envoy.yaml)"
    kubectl patch deployment shippingservice --patch "$(cat patch-envoy.yaml)"
    ```
    >L7 flow logs will require a few minutes to generate.

4.  Review L7 logs

    The HTTP logs can be reviewed from `Service Graph` and then clicking the `HTTP` tab. Details of each flow can be reviewed by drilling down into the flow record

    <img src="../img/service-graph-l7.png" alt="Service Graph L7" width="100%"/>

[Next -> Module 6](../modules/using-security-controls.md)




