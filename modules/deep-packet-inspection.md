# Module 13: Deep packet inspection

**Goal:** Configure deep packet inspection for sensitive workloads to allow Calico inspect packets and alert on suspicious traffic.

## Steps

1. Configure deep packet inspection (DPI) resource.

    Navigate to `demo/70-deep-packet-inspection` and review YAML manifests that represent DPI resource definition. A DPI resource is usually deployed to watch traffic for entire namespace or specific pods within the namespace using label selectors.

    Deploy DPI resource definition to allow Calico inspect packets bound for `dev/nginx` pods.

    ```bash
    kubectl apply -f demo/70-deep-packet-inspection/nginx-dpi.yaml
    ```

    >Once the `PacketCapture` resource is deployed, Calico starts capturing packets for all endpoints configured in the `selector` field.

    Wait until all DPI pods become `Ready`

    ```bash
    watch kubectl get po -n tigera-dpi
    ```

2. Simulate malicious request.

    Query `dev/nginx` application with payload that triggers a Snort rule alert.

    ```bash
    kubectl -n dev exec -t centos -- sh -c "curl http://nginx-svc -H 'User-Agent: Mozilla/4.0' -XPOST --data-raw 'smk=1234'"
    ```

3. Review alerts.

    Navigate to the Alerts view in Tigera UI and review alerts triggered by DPI controller. Calico DPI controller uses [Snort](https://www.snort.org/) signatures to perform DPI checks.

Congratulations! You have finished all the labs in the workshop.
