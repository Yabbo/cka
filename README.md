# CKA 
Certified Kubernetes Administrator notes and templates


### Switch namespace on cli to dev

``` kubectl config set-context $(kubectl config current-context) --namespace=dev ```


### List pods in ALL namespaces

``` kubectl get pods --all-namespaces ```


## Services

### Node Port

Services will be created across the entire cluster matching all pods in the selector
- Range can only be between 30000 and 32767


