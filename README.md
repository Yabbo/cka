# CKA 
Certified Kubernetes Administrator notes and templates

``` --dry-run ``` By default as soon as the command is run, the resource will be created. If you simply want to test your command , use the ``` --dry-run ``` option. This will not create the resource, instead, tell you weather the resource can be created and if your command is right.

To get the YAML file of a running pod/service/deployment... use the following syntax

```kubectl get deployment,service,pod yourapp -o yaml --export > yourapp.yaml```

``` -o yaml ``` This will output the resource definition in YAML format on screen.

Use the above two in combination to generate a resource definition file quickly, that you can then modify and create resources as required, instead of creating the files from scratch.

---
## Upgrading Nodes

* use ``kubectl drain node`` to drain it of pods and mark it unscheduleable.
* preform os or other upgrade tasks 
* one the node is back online use the command ```kubectl uncordon node``` to allow it to be scheduable again

<b>NOTE:</b> you can also use the ```kuibectl cordon node``` command to make sure new pods are not scheduled onto it but it will not drain it of existing pods. 

---
## Editing
Edit any API resource in your preferred editor.

Edit the service named docker-registry
``` kubectl edit svc/docker-registry ```

 Use an alternative editor
``` KUBE_EDITOR="nano" kubectl edit svc/docker-registry ``` 

The ```kubectl edit``` command will open any configuration currently running within your kubernetes cluster for editing. Appon saving the changes will be applied to the cluster.

---
## POD

### List pods in ALL namespaces

``` kubectl get pods --all-namespaces ```

### Create an NGINX Pod

``` kubectl run --generator=run-pod/v1 nginx --image=nginx ``` 



### Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)

``` kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml ```

---
## Deployment
### Create a deployment

``` kubectl create deployment --image=nginx nginx ```



### Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

``` kubectl create deployment --image=nginx nginx --dry-run -o yaml ```



### Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)

``` kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml ```

The usage ``` --generator=deployment/v1beta1 ``` is deprecated as of Kubernetes 1.16. The recommended way is to use the ``` kubectl create ``` option instead.



### IMPORTANT:

``` kubectl create deployment ``` does not have a ``` --replicas ``` option. You could first create it and then scale it using the ``` kubectl scale ``` command.



### Save it to a file - (If you need to modify or add some other details)

``` kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml > nginx-deployment.yaml ```



OR

``` kubectl create deployment --image=nginx nginx --dry-run -o yaml > nginx-deployment.yaml ```

You can then update the YAML file with the replicas or any other field before creating the deployment.

---
## Services

### Node Port

Services will be created across the entire cluster matching all pods in the selector
- Range can only be between 30000 and 32767 

### Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379

``` kubectl expose pod redis --port=6379 --name redis-service --dry-run -o yaml ```

(This will automatically use the pod's labels as selectors)

Or

``` kubectl create service clusterip redis --tcp=6379:6379 --dry-run -o yaml ```  (This will not use the pods labels as selectors, instead it will assume selectors as <b>app=redis</b>. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)



### Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:

``` kubectl expose pod nginx --port=80 --name nginx-service --dry-run -o yaml ```

(This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

``` kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml ```

(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

---
## Namespaces

### Switch namespace on cli to dev

``` kubectl config set-context $(kubectl config current-context) --namespace=dev ```

---
## Logs

Accessing logs from a container within kubernetes is as simple as running ```kubectl logs``` You can even use the ```-f``` flag to follow the logs

```kubectl logs -f pod-name container-name```

---
## Secrets
There are many ways to create secrets but if you create a file you must base64 encode the keys. this is not secure but the practice of using a secret file makes it more secure as its only allowed in pods the request it and not exposed to the entire cluster.

The ```kubectl get secrets``` command will have a columb of DATA which is how many keys are stored in the secret.

The easiest way to create a secret file with the following values it to run this command.
* Secret Name: db-secret
* Secret 1: DB_Host=sql01
* Secret 2: DB_User=root
* Secret 3: DB_Password=password123

 it to run this command.
 
```kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123```

you can then reference the secret in a pod definition as follows. 

```
apiVersion: v1
kind: Pod
metadata:
 labels:
  name: webapp-pod
 name: webapp-pod
spec:
 containers:
 - image: kodekloud/simple-webapp-mysql
   imagePullPolicy: Always
   name: webapp
   envFrom:
   - secretRef:
      name: db-secret
```

---
### Reference:

https://kubernetes.io/docs/reference/kubectl/conventions/

