# Glasgow University - Skills Workshop
Topic:&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kubernetes & Helm \
Date:&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;21. November 2019 \
Author:&nbsp;&nbsp;&nbsp;&nbsp;Nicola M&ouml;ssner

## Kubernetes

### What is it?
- Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.

### Why do we use it at ResDiary?
- Containerisation, shorter deploy times, automated scaling/updates/rollback, built-in logging/metrics, etc. 

### Terminology
- Node: A node is a worker machine in Kubernetes. A node may be a VM or physical machine, depending on
  the cluster. Each node contains the services necessary to run pods and is managed by the master components
- Pod: Pods are the smallest deployable units of computing that can be created and managed in Kubernetes.
  A Pod is a group of one or more containers (such as Docker containers), with shared storage/network,
  and a specification for how to run the containers. A Pod models an application-specific “logical host”
- Service: An abstract way to expose an application running on a set of Pods as a network service.
  Kubernetes gives Pods their own IP addresses and a single DNS name for a set of Pods, and can load-balance
  across them.
- Deployment: A Deployment provides declarative updates for Pods. You describe a desired state in a Deployment,
  and the Deployment Controller changes the actual state to the desired state at a controlled rate.
- Kubectl: Kubectl is a command line interface for running commands against Kubernetes clusters

## Helm
- Allows you to define all your resources in yaml files as a chart. Provides you further with functionality to
  install/update/delete that chart in the desired Kubernetes cluster and namespace.
```bash
# Create a new helm chart
helm create mychart
```

## Demo

### 1. Show all resources in a cluster
```bash
kubectl get all
```

### 2. Different ways of creating a pod running a busybox-container
```bash
# A single line on the command line
kubectl run -it --rm busybox-container --image=busybox --restart=Never -- sh

# Apply a yaml file
kubectl apply -f .\mychart\templates\pod-busybox.yaml
kubectl get pods
kubectl exec -it busybox-pod sh

# Use helm to deploy a chart
helm install .\mychart\ --name=mychart-1
# If the release already exists, delete/purge it first to start anew
helm delete --purge mychart-1
# Use upgrade from now on
helm upgrade mychart-1 .\mychart\ --install
```

### 3. Create a deployment
- Definiton in yaml file `deployment-nginx.yaml`
- Enable `deploymentNginx` in `values.yaml`
```bash
helm upgrade mychart-1 .\mychart\
```

### 4. Use busybox to access web server pods
- Two pods running the nginx webserver are now running. We can access them through their IPs.
```bash
kubectl get pods -o wide
# Enter command line of running busybox
kubectl exec -it busybox-pod sh
# Alternatively, you can directly execute the command, passing it into the busybox container
kubectl exec -it busybox-pod -- wget <POD_IP>:<PORT>

# To check if the web server is running
wget <POD_IP>:<PORT> # Example: wget 10.1.75.23:80
ls
cat index.html
# Alternatively, port-forward to it and check http://localhost:30909 in your browser
kubectl port-forward svc/<POD_NAME> 30909:80
```

### 5. Delete and observe a pod
- Open new terminal window to observe pods.
```
kubectl get pods -o wide -w
```
- In first terminal window
```
kubectl delete pod <POD_NAME>
```
- If a pod dies it is recreated with a different IP. Why could this be problematic?

### 6. Use a service to access the application running across multiple pods
- Definiton in yaml file `service-nginx.yaml`
- Enable `serviceNginx` in `values.yaml`
```bash
helm upgrade mychart-1 .\mychart\
# Observe 'SELECTOR' label
kubectl get services -o wide
# Observe 'Endpoints'
kubectl describe service my-nginx-service 
# Compare Endpoints to the pod's IPs
kubectl get pods -o wide 
# Port-forward to the service and check http://localhost:30909 in your browser
kubectl port-forward svc/my-nginx-service 30909:80 
```

### 7. Start a new application and access it in the web-browser
- Enable `serviceTodoApp` and `deploymentTodoApp` in `values.yaml`
```bash
helm upgrade mychart-1 .\mychart\
# Port-forward to the service and check http://localhost:30908 in your browser
kubectl port-forward svc/todo-app-service 30908:80 
```

### 8. Scale a deployment
```bash
# Scale deploymeny to zero pods
kubectl scale deployment todo-app-deployment --replicas=0
# Observe that the 'todo-app' has no running pods anymore
kubectl get pods
# Scale back up
kubectl scale deployment todo-app-deployment --replicas=4
# Observe the pods again
kubectl get pods -w
# Restart port-forwarding
kubectl port-forward svc/todo-app-service 30908:80 
# Introduce a horizontal-pod-autoscaler to automate this in response to varying workload
kubectl autoscale deployment todo-app-deployment --min=2 --max=6
```

### 9. Create a cron job (schedules jobs regularly)
- Definiton in yaml file `cronjob.yaml`
- Enable `cronjob` in `values.yaml`
```bash
helm upgrade mychart-1 .\mychart\
```

## Further Resources
- Official tutorials (https://kubernetes.io/docs/tutorials/)​
    - Minikube (https://github.com/kubernetes/minikube)​
    - Katacoda (https://www.katacoda.com/courses/kubernetes)​
- Google Kubernetes Engine (https://cloud.google.com/kubernetes-engine/)​
- Kubectl cheat sheet (https://kubernetes.io/docs/reference/kubectl/cheatsheet/)​


## Sources
- https://kubernetes.io/docs/concepts/ (for Terminology)
- 
