# Creating objects in kubernetes in declarative way:
## This method of creating objects in kubernetes is called declarative way.
## In this method we create a yaml file and then apply it using kubectl command.
## Similar to docker-compose file.
## In this file we define the desired state of the object.
## The kubernetes controller will then work to make the current state of the object match the desired state.

- After setting up the deployment.yaml file with our deployment object+pod specification, we can create the deployment using the kubectl apply command.
- The kubectl apply command will create the deployment object in the kubernetes cluster.
- The kubectl apply command will also create the pod object in the kubernetes cluster.
```bash
kubectl apply -f deployment.yaml # -f stands for file
```
