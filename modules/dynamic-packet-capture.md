# Module 13: Dynamic packet capture

**Goal:** Configure packet capture for specific pods and review captured payload.

## Steps

1. Configure packet capture.

    >One can initiate a packet capture from the Service Graph view by right-clicking on a node in the graph and choosing the `Initiate packet capture` option.

    Navigate to `demo/60-packet-capture` and review YAML manifests that represent packet capture definition. Each packet capture is configured by deploing a `PacketCapture` resource that targets endpoints using `selector` and `labels`.

    Deploy packet capture definition to capture packets for `dev/nginx` pods.

    ```bash
    kubectl apply -f demo/60-packet-capture/nginx-pcap.yaml
    ```

    >Once the `PacketCapture` resource is deployed, Calico starts capturing packets for all endpoints configured in the `selector` field.

2. Install `calicoctl` CLI

    The easiest way to retrieve captured `*.pcap` files is to use [calicoctl](https://docs.tigera.io/maintenance/clis/calicoctl/) CLI.

    ```bash
    CALICO_VERSION=$(kubectl get clusterinformation default -ojsonpath='{.spec.cnxVersion}')
    # download and configure calicoctl
    curl -o calicoctl -O -L https://docs.tigera.io/download/binaries/${CALICO_VERSION}/calicoctl
    chmod +x calicoctl
    sudo mv calicoctl /usr/local/bin/
    calicoctl version
    ```

3. Fetch and review captured payload.

    >The captured `*.pcap` files are stored on the hosts where pods are running at the time the `PacketCapture` resource is active.

    Retrieve captured `*.pcap` files and review the content.

    ```bash
    # get pcap files
    calicoctl captured-packets copy nginx-pcap --namespace dev

    ls dev-nginx*
    # view *.pcap content
    tcpdump -Ar dev-nginx-XXXXXX.pcap
    tcpdump -Xr dev-nginx-XXXXXX.pcap
    ```

4. Stop packet capture

    Stop packet capture by removing the `PacketCapture` resource.

    ```bash
    kubectl delete -f demo/60-packet-capture/nginx-pcap.yaml
    ```

>Note Packet Captures can also be created and scheduled directly from the Calico UI. Follow the Service Graph method for this alternative [procedure](https://docs.tigera.io/visibility/packetcapture#access-packet-capture-files-via-service-graph).

[Next -> Module 14](../modules/deep-packet-inspection.md)
