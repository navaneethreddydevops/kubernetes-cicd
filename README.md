# Kubernetes Cluster

# Get commands with basic output
```
kubectl get services                          # List all services in the namespace
kubectl get pods --all-namespaces             # List all pods in all namespaces
kubectl get pods -o wide                      # List all pods in the current namespace, with more details
kubectl get deployment my-dep                 # List a particular deployment
kubectl get pods                              # List all pods in the namespace
kubectl get pod my-pod -o yaml                # Get a pod's YAML
```
# Describe commands with verbose output
```
kubectl describe nodes my-node
kubectl describe pods my-pod
```
# List Services Sorted by Name

```
kubectl get services --sort-by=.metadata.name
```

# List pods Sorted by Restart Count
```
kubectl get pods --sort-by='.status.containerStatuses[0].restartCount'
```
# List PersistentVolumes sorted by capacity
```
kubectl get pv --sort-by=.spec.capacity.storage
```
# Get the version label of all pods with label app=cassandra
```
kubectl get pods --selector=app=cassandra -o \
  jsonpath='{.items[*].metadata.labels.version}'
```
# Retrieve the value of a key with dots, e.g. 'ca.crt'
```
kubectl get configmap myconfig \
  -o jsonpath='{.data.ca\.crt}'
```
# Get all worker nodes (use a selector to exclude results that have a label
# named 'node-role.kubernetes.io/master')
```
kubectl get node --selector='!node-role.kubernetes.io/master'
```
# Get all running pods in the namespace
```
kubectl get pods --field-selector=status.phase=Running
```
# Get ExternalIPs of all nodes
```
kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="ExternalIP")].address}'
```
# List Names of Pods that belong to Particular RC
# "jq" command useful for transformations that are too complex for jsonpath, it can be found at https://stedolan.github.io/jq/

```
sel=${$(kubectl get rc my-rc --output=json | jq -j '.spec.selector | to_entries | .[] | "\(.key)=\(.value),"')%?}
echo $(kubectl get pods --selector=$sel --output=jsonpath={.items..metadata.name})
```
# Show labels for all pods (or any other Kubernetes object that supports labelling)
```
kubectl get pods --show-labels
```
# Check which nodes are ready
```
JSONPATH='{range .items[*]}{@.metadata.name}:{range @.status.conditions[*]}{@.type}={@.status};{end}{end}' \
 && kubectl get nodes -o jsonpath="$JSONPATH" | grep "Ready=True"
```
# List all Secrets currently in use by a pod
```
kubectl get pods -o json | jq '.items[].spec.containers[].env[]?.valueFrom.secretKeyRef.name' | grep -v null | sort | uniq
```
# List all containerIDs of initContainer of all pods
# Helpful when cleaning up stopped containers, while avoiding removal of initContainers.
```
kubectl get pods --all-namespaces -o jsonpath='{range .items[*].status.initContainerStatuses[*]}{.containerID}{"\n"}{end}' | cut -d/ -f3
```
# List Events sorted by timestamp
```
kubectl get events --sort-by=.metadata.creationTimestamp
```
# Compares the current state of the cluster against the state that the cluster would be in if the manifest was applied.
```
kubectl diff -f ./my-manifest.yaml
```

### Updating Resources
```
kubectl set image deployment/frontend www=image:v2               # Rolling update "www" containers of "frontend" deployment, updating the image
kubectl rollout history deployment/frontend                      # Check the history of deployments including the revision 
kubectl rollout undo deployment/frontend                         # Rollback to the previous deployment
kubectl rollout undo deployment/frontend --to-revision=2         # Rollback to a specific revision
kubectl rollout status -w deployment/frontend                    # Watch rolling update status of "frontend" deployment until completion
kubectl rollout restart deployment/frontend                      # Rolling restart of the "frontend" deployment
cat pod.json | kubectl replace -f -                              # Replace a pod based on the JSON passed into std
```
# Force replace, delete and then re-create the resource. Will cause a service outage.
```
kubectl replace --force -f ./pod.json
```
# Create a service for a replicated nginx, which serves on port 80 and connects to the containers on port 8000
```
kubectl expose rc nginx --port=80 --target-port=8000
```
# Update a single-container pod's image version (tag) to v4
```
kubectl get pod mypod -o yaml | sed 's/\(image: myimage\):.*$/\1:v4/' | kubectl replace -f -
kubectl label pods my-pod new-label=awesome                      # Add a Label
kubectl annotate pods my-pod icon-url=http://goo.gl/XXBTWq       # Add an annotation
kubectl autoscale deployment foo --min=2 --max=10                # Auto scale a deployment "foo"
```

