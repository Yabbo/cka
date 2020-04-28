# CKA 
Certified Kubernetes Administrator notes and templates


### Switch namespace on cli to dev

``` kubectl config set-context $(kubectl config current-context) --namespace=dev ```


### List pods in ALL namespaces

``` kubectl get pods --all-namespaces ```
