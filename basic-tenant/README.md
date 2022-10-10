### What have we done here?

- Pods run on specific labeled nodes only.
- Pods have good QoS, which means once they are scheduled they are guaranteed to have required resources.
- Demonstrated blocking incoming traffic and outgoing traffic using NetworkPolicies using labels. So here only app namespaces would be able to communicate with each other.

### Setup nodes for application

```bash
kubectl label nodes minikube application=product
```

### Deploy the application

```bash
helm upgrade --install basic-tenant --values basic-tenant/values.yaml --debug basic-tenant
```
