### Get resources by label

```
kubectl get all -l "env in (prod)"
```

### List of common kubectl command

##### Common options for `kubectl get|run|delete` 
- Adding the option `-o wide` to the command to see more details.
- Adding the option `-o yaml` to render yaml definition of the resource.
- Adding the option `-n <namespace>` or `--namespace <namespace>` to choose specific namespace.
- Adding the option `--all-namespaces` or `-A` to include resources from all namespaces.
- Adding the option `--show-labels` to display the configured labels on the resources.
- Adding the option `--no-headers` to suppress headers in the output, add a little convenience when counting rows, i.e. `wc -l`.

|Command|Alternative|Description|
|----|-----|-----|
|kubectl apply -f \<definition file\>||Create or update resources from the definition file|
|kubectl run --image \<image\>[\<:tag\>] <name>||Run a new pod with the name from specified image|
|kubectl delete \<resource type\> \<resource name\>||Delete the resource| 
|kubectl get pods||List pods |
|kubectl get replicaset|kubectl get rs|List replicaset|
|kubectl get deployments|kubectl get deploy|List deployments|
|kubectl get services||List services|
|kubectl get serviceaccount|kubectl get sa|List service accounts|
|kubectl get roles||List roles|
|kubectl get rolebindings||List role bindings|
|kubectl get clusterroles||List cluster roles|
|kubectl get clusterrolebindings||List cluster role bindings|
|kubectl get storageclass|kubectl get sc||List storage classes|
|kubectl get persistentvolumes|kubectl get pv|List persistent volumes|
|kubectl get persistentvolumeclaims|kubectl get pvc|List persistent volume claims|
|kubectl get configmaps|kubectl get cm|List configmaps|
|kubectl get secrets||List secrets|
|kubectl get ingress||List ingresses|
|kubectl get networkpolicies||List network policies|
|kubectl api-resources||List resources with corresponding API versions|
|kubectl get certificatesigningrequests|kubectl get csr|List certificate signing requests|
|kubectl certificate approve \<name\>||Approve certificate signing request|
|kubectl certificate deny \<name\>||Reject certificate signing request|
|kubectl drain \<node name\>||Migrate running pods on the node to other nodes and stop it from the scheduling new pods|
|kubectl cordon \<node name\>||Stop the node from scheduling new pods|
|kubectl uncordon \<node name\>||Enable the node for scheduling new pods|
|kubectl config current-context||Display the name of current context|
|kubectl config use-context \<context name\>||Switch to the specified context|
|kubectl replace --force -f \<file\>||Force replacing the resources, i.e. delete and recreate. Also useful when edit the resource in-place|

### Shothand kubectl commands to create resource definitions as template

- `kubectl run --image redis:alpine redis  --labels="tier=db" --dry-run=client -o yaml` 
- `kubectl create deployment test -n default --dry-run=client --image=nginx -o yaml`
- `kubectl create service nodeport test -n default --dry-run=client --tcp=8080:8282 --node-port=30080 -o yaml`
- `kubectl create service clusterip redis-service --tcp=6379:6379 --dry-run=client -o yaml`

### Deployments

- `kubectl set image deployment frontend simple-webapp=kodekloud/webapp-color:v2`
- `kubectl create secret docker-registry private-reg-cred --docker-server myprivateregistry.com:5000 --docker-username dock_user --docker-password dock_password --docker-email dock_user@myprivateregistry.com`

### Roles

- `kubectl create role developer --verb="list,create,delete" --resource="pod" `
- `kubectl create rolebinding dev-user-binding --user=dev-user --role=dev-user-binding`
- `kubectl create clusterrole nodes --verb="get,watch,list,create,delete" --resource="nodes"`
- `kubectl create clusterrolebinding nodes --clusterrole=nodes --user="michelle"`

