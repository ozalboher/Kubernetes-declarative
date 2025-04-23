# Creating objects in kubernetes in declarative way:
## This method of creating objects in kubernetes is called declarative way.
## In this method we create a yaml file and then apply it using kubectl command.
## Similar to docker-compose file.
## The yaml file name is totally up to you, but it is a good practice to name it according to the object you are creating.
## The yaml file should be in the same directory as your dockerfile and app.js file.
## In this file we define the desired state of the object.
## The kubernetes controller will then work to make the current state of the object match the desired state.
## Both yaml files covered here (the deployment.yaml and service.yaml) can actually be merged into one file, if you want to. And you can call it "master-deployment.yaml" or something like that. And make sure to separate the objects with kubernetes specific syntax of 3 dashes. But for the sake of simplicity and clarity, we will keep them separate.
## selector field is a crucial field. it is essentially connecting the pods to the deployment/service object.
# Deployment object yaml file:(see deployment.yaml for the full code and explanation)
- After setting up the deployment.yaml file with our deployment object+pod specification, we can create the deployment using the kubectl apply command.
- The kubectl apply command will create the deployment object in the kubernetes cluster.
- The kubectl apply command will also create the pod object in the kubernetes cluster.
```bash
kubectl apply -f deployment.yaml # -f stands for file
```
* Note that in the object "deployment.yaml" file, the pod is defined under what is called "template" field. and inside that template there are 2 nested crucial settings you must define:
- 1. the first one is "metadata" (with a nested "labels" for naming the pod) gave the pod a name(label) in the template section under the metadata section.
- 2. the second one is "spec" (with a nested "containers" for defining the container image and under containers another nested "name" & "image") to specify the image(url) to be used in the pod.

* * Another thing to note is that the "selector" field is important to match the labels in the template section(labels) with the labels in the selector section(matchLabels). This is how kubernetes knows which pods belong to which deployment. And they must match to be written exactly in both sections(if given 2 labels write at least the first one exactly as it appears in the pod section). But any extra labels in the pod are perfectly fine as long as you write it in the selector->matchExpression. and not matchLabels. The matchExpression is used to specify a set of labels that the deployment should match. This allows for more flexibility in matching labels. For example, you can use the matchExpression to specify that the deployment should match all pods with a certain label, or all pods with a certain label and a certain value. This is useful if you want to create a deployment that matches multiple pods with different labels. here is an example of how to use the matchExpression to specify a set of labels that the deployment should match:
```yaml
selector:
 matchExpressions:
  - {key: app, operator: In, values: [backend1, backend2]}
## Any pod with the label "app" (a must) and value "backend1" or "backend2" will be matched by the deployment.
```
* * In the service.yaml object in "selector" field, you type labels directly unlike the deployment. And here you might just specify the first label. And it will know how to associate to it - and in case you have multiple pods that shares the same main label it will associate to all of them. So this can be handy if you want to set a service to apply to multiple pods. 
 - Under *metadata* section of the deployment.yaml - Besides a name for the deployment - Adding label for the deployment, can be used to group multiple deployments together,
 - and then target ALL of them at once using this label.
 ```bash
 kubectl delete deployments -l group=groupA # delete all deployments with the label group=groupA, or type "services" instead of "deployments" to delete all services with the label group=groupA.
 ```

# Service object yaml file:(see service.yaml for the full code and explanation)
- Now we will need a service object to expose our deployment object.
- The service object will have a separate yaml file.(service.yaml)
- The service.yaml will be defined in a similar way to the deployment.yaml file but in the spec field we have:
1. "selector" field we will specify the label of the pod we want to expose. And the first label is enough to be specified in the selector field to let it know the pod to be associated to.(as mentioned above). This allows for flexibility in managing multiple pods that share the same main label.
2. "ports" field where we define under: " -protocol:TCP" the protocol to be used, and under "port" the outside world port number to be exposed(80), and under "targetPort" the port of the internal container to be exposed(8080 - what is also defined in app.js).
3. "type" field where we define the type of service to be used. Under the same level of the "ports" field we define the type if it is:
- "ClusterIP" - this is the default type and it will expose the service on a cluster-internal IP. This means that the service will only be accessible from within the cluster.
- "NodePort" - this will expose the service on the worker node with an IP at a static port. This means that the service will be accessible from outside the cluster using the node's IP and the specified port.
- "LoadBalancer" - most commonly use, to get outside world access. this will expose the service externally using a cloud provider's load balancer. This means that the service will be accessible from outside the cluster using the load balancer's IP plus incoming traffic will be distributed equally.
## To execute the service.yaml file we will use the same command as the deployment.yaml file:
```bash
kubectl apply -f service.yaml 
```
- Now you will see the service with 
```bash
kubectl get services
```
* And again just like in the imperative way, after we created the service, since we are so far working inside the minikube virtual cluster we will need to use the minikube service command to access the service from outside the cluster.
```bash
minikube service <service-name> # replace <service-name> with the name of your service(e.g. "backend" as written in the name field under metadata section in the service.yaml file).
```
# Cleaning things up:
- To delete the deployment and service objects we can use the kubectl delete command.
```bash
kubectl delete -f deployment.yaml # delete all resources created by the deployment object (not the file obviously)
``` 
- Also in order to change/add new things in the yaml file, you can edit the file directly and then simply reapply it using the kubectl apply command! easy peasy! (even change the image url to a new one).
```bash
kubectl apply -f deployment.yaml # reapply the file to update the changes
```
* Though it is worth mentioning you can still use the imperative way to delete the deployment and service objects if you want to.
```bash
kubectl delete deployment <deployment-name> # replace <deployment-name> with the name of your deployment(e.g. "backend" as written in the name field under metadata section in the deployment.yaml file).
kubectl delete service <service-name> # replace <service-name> with the name of your service(e.g. "backend" as written in the name field under metadata section in the service.yaml file).
```
# livenessProbe: use this as a health check, if you want to check if the pod is alive or not. This is useful if you want to make sure that the pod is running and healthy. If the pod is not healthy, kubernetes will restart it automatically.

