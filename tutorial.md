## Helm
- Allows you to define all your resources in yaml files as a chart. Provides you further with functionality to
  install/update/delete that chart in the desired Kubernetes cluster and namespace.
```bash
# Create a new helm chart
helm create mychart
```
### Different ways of creating a pod running a busybox-container
```bash
# A single line on the command line
kubectl run -it --rm busybox-pod --image=busybox --restart=Never -- sh

# Apply a yaml file
kubectl apply -f .\mychart\templates\pod-busybox.yaml

# Use helm to deploy a chart
helm install --name=mychart .\mychart\
# Use upgrade from now on
helm upgrade mychart .\mychart\
```

## Tutorial

### Show all resources in a cluster
```bash
kubectl get all
```

### Create a deployment
- Definiton in yaml file `deployment-nginx.yaml`
- Enable `deploymentNginx` in `values.yaml`
```bash
helm upgrade mychart .\mychart\
```

### Use busybox to access web server pods
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
kubectl port-forward pod/<POD_NAME> 30909:80
```

### Delete and observe a pod
- Open new terminal window to observe pods.
```
kubectl get pods -o wide -w
```
- In first terminal window
```
kubectl delete pod <POD_NAME>
```
- If a pod dies it is recreated with a different IP. Why could this be problematic?

### Use a service to access the application running across multiple pods
- Definiton in yaml file `service-nginx.yaml`
- Enable `serviceNginx` in `values.yaml`
```bash
helm upgrade mychart .\mychart\
# Observe 'SELECTOR' label
kubectl get services -o wide
# Observe 'Endpoints'
kubectl describe service my-nginx-service 
# Compare Endpoints to the pod's IPs
kubectl get pods -o wide 
# Port-forward to the service and check http://localhost:30909 in your browser
kubectl port-forward svc/my-nginx-service 30909:80 
```

### Start a new application and access it in the web-browser
- Enable `serviceTodoApp` and `deploymentTodoApp` in `values.yaml`
```bash
helm upgrade mychart .\mychart\
# Port-forward to the service and check http://localhost:30908 in your browser
kubectl port-forward svc/todo-app-service 30908:80 
```

### Scale a deployment
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

### Create a cron job (schedules jobs regularly)
- Definiton in yaml file `cronjob.yaml`
- Enable `cronjob` in `values.yaml`
```bash
helm upgrade mychart .\mychart\
```

## Further Resources
- Official tutorials (https://kubernetes.io/docs/tutorials/)​
    - Minikube (https://github.com/kubernetes/minikube)​
    - Katacoda (https://www.katacoda.com/courses/kubernetes)​
- Google Kubernetes Engine (https://cloud.google.com/kubernetes-engine/)​
- Kubectl cheat sheet (https://kubernetes.io/docs/reference/kubectl/cheatsheet/)​