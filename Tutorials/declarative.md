# Creating objects in kubernetes in declarative way:
## This method of creating objects in kubernetes is called declarative way.
## In this method we create a yaml file and then apply it using kubectl command.
## Similar to docker-compose file.
## In this file we define the desired state of the object.
## The kubernetes controller will then work to make the current state of the object match the desired state.

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

* * Another thing to note is that the "selector" field is important to match the labels in the template section(labels) with the labels in the selector section(matchLabels). This is how kubernetes knows which pods belong to which deployment. And they must match to be written exactly in both sections(if given 2 labels write them both). 
* * In the service.yaml object youre kind of more flexible with the "selector" field, and you just can specify the first label. And it will know how to associate to it - and in case you have multiple pods that shares the same main label it will associate to all of them. So this can be handy if you want to set a service to apply to multiple pods. 

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