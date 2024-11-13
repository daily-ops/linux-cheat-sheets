### Get resources by label

```
kubectl get all -l "env in (prod)"
```

### List of common kubectl command

##### Common options for `kubectl get|run|delete` 
- Adding the option `-o wide` to the command to see more details.
- Adding the option `-o yaml` to render yaml definition of the resource.
- Adding the option `-n <namespace>` or `--namespace <namespace>` to choose specific namespace.
- Adding the option `--all-namespaces` to include resources from all namespaces.

|Command|Alternative|Description|
|----|-----|-----|
|kubectl apply -f \<definition file\>||Create or update resources from the definition file|
|kubectl run --image \<image\>[\<:tag\>] <name>||Run a new pod with the name from specified image|
|kubectl delete \<resource type\> \<resource name\>||Delete the resource| 
|kubectl get pods||List pods |
|kubectl get deployments||List deployments|
|kubectl get services||List services|
|kubectl get serviceaccount|kubectl get sa|List service accounts|
|kubectl get roles||List roles|
|kubectl get rolebindings||List role bindings|
|kubectl get clusterroles||List cluster roles|
|kubectl get clusterrolebindings||List cluster role bindings|
|kubectl get persistentvolumes|kubectl get pv|List persistent volumes|
|kubectl get persistentvolumeclaims|kubectl get pvc|List persistent volume claims|
|kubectl get configmaps||List configmaps|
|kubectl get secrets||List secrets|
|kubectl get ingress||List ingresses|
|kubectl api-resources||List resources with corresponding API versions|
|kubectl get certificatesigningrequests|kubectl get csr|List certificate signing requests|
|kubectl certificate approve \<name\>||Approve certificate signing request|
|kubectl certificate deny \<name\>||Reject certificate signing request|
|kubectl drain \<node name\>||Migrate running pods on the node to other nodes and stop it from the scheduling new pods|
|kubectl cordon \<node name\>||Stop the node from scheduling new pods|
|kubectl uncordon \<node name\>||Enable the node for scheduling new pods|
