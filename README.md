# k8s-multi-tenant
A sample usecase how one can secure multi-namespace apps on shared k8s cluster.

## Things to consider

### Network

- App namespaces should be isolated from all other namespaces so that no other service can access the ports of these apps.

This can be done by adding the desired Ingress+Egress network policies to the app namespaces. The catch here is that the k8s CNI should be able to support network policies.

### DNS

- DNS lookups should not be allowed from other namespaces. Though this does not cause any issue but its a good to have.
- Do not depend on cluster DNS or default DNS as its possible that it can contain some malformed entries.

So either configure deployments to query external DNS or setup a DNS for app namespaces if needed.

### Storage

- Based on usecase, its possible that pods need persistent storage, so make sure that those storage are not shared with anyother pods outside of the app.

This can be solved by having decidated nodes for app's pods or not allowing malicious pods to run in app's namespace so that they do not use PVC.

### IAM

- Setup an exclusive RBAC roles for accessing different namespaces and verbs.

Make use of ClusterRole, Groups, ServiceAccounts, etc to restrict more than required access to entities.

### Resources

- Pods might get evicted due to multiple reasons like: noisy neighbour, problamatic node, priority, etc.

Have separate node groups for apps. If having separate node groups, one can also have a dedicated cluster autoscalar for this so that the main autoscalar does not involve here for managing these nodes, and other apps can scale up down however they want without impacting these apps.

Mandate the use of pod resource requests and limits by setting up Quotas. Make sure that requests and limits are close to each other so that pods have high QoS and less likely chance of eviction.

### Bad Code

- Try to isolate apps from host which can have some vulnerabilities or avoid from running any malicious commands.

Explore some vulnerabilities agents or tools like gVisor or kata containers.

- Avoid running a malicious image.

Try to pull images as much as possible and not use IfNotPresent or Never option.

Validate if images are signed with the right signature by implementing it in admission controller. [Example](https://kubernetes.io/docs/tasks/administer-cluster/verify-signed-images/)

