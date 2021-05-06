# Module 5: Securing East-West traffic

**Goal:** Segment connections within Kubernetes cluster to protect East-West traffic.

## Steps

1. Deploy application stacks `dev` and `hipstershop`.

    ```bash
    # deploy dev app stack
    kubectl apply -f demo/dev/app.manifests.yaml

    # deploy hipstershop app stack
    kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/microservices-demo/master/release/kubernetes-manifests.yaml
    ```

2. Test connectivity between application components and across application stacks.

    ```bash
    # test connectivity within dev namespace
    kubectl -n dev exec -t centos -- sh -c 'curl -m 3 -sI http://nginx-svc 2>/dev/null | grep -i http'

    # test connectivity within hipstershop namespace
    kubectl -n hipstershop exec -it $(kubectl -n hipstershop get po -l app=checkoutservice -ojsonpath='{.items[0].metadata.name}') -c server -- sh -c 'wget www.google.com -O /dev/null -S'

    # test connectivity from dev namespace to hipstershop namespace
    kubectl -n dev exec -t centos -- sh -c 'curl -m 3 -sI http://frontend.hipstershop 2>/dev/null | grep -i http'

    # test connectivity from hipstershop namespace to dev namespace
    kubectl -n hipstershop exec -it $(kubectl -n hipstershop get po -l app=checkoutservice -ojsonpath='{.items[0].metadata.name}') -c server -- sh -c 'wget nginx-svc.dev -O /dev/null -S'
    ```

    All of these tests should succeed if there are no policies in place to govern the traffic between `dev` and `hipstershop` namespaces.

3. Apply network policies to control East-West traffic.

    ```bash
    ```

[Next -> Module 6](../modules/***.md)
