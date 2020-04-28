# CKA 
Certified Kubernetes Administrator notes and templates

``` --dry-run ``` By default as soon as the command is run, the resource will be created. If you simply want to test your command , use the ``` --dry-run ``` option. This will not create the resource, instead, tell you weather the resource can be created and if your command is right.

``` -o yaml ``` This will output the resource definition in YAML format on screen.

Use the above two in combination to generate a resource definition file quickly, that you can then modify and create resources as required, instead of creating the files from scratch.



### Switch namespace on cli to dev

``` kubectl config set-context $(kubectl config current-context) --namespace=dev ```


### List pods in ALL namespaces

``` kubectl get pods --all-namespaces ```


## Services

### Node Port

Services will be created across the entire cluster matching all pods in the selector
- Range can only be between 30000 and 32767


