## Kubernetes

### What is it?
- Orchestrate containers

### How does it work?
- Terminology: Nodes, Pods, Services, Deployments
- Diagram

### Why do we use it?
- 

## Helm
- Define resources in yaml files
```bash
# Create a new helm chart
helm create testchart
```

## Demo

### Show cluster with no resources
```bash
kubectl get all
```

### Different ways of creating a pod running a busybox-container
```bash
# On the command line
kubectl run -it --rm busybox-container --image=busybox --restart=Never -- sh

# Apply yaml file
kubectl apply -f .\mychart\templates\pod-busybox.yaml
kubectl get pods
kubectl exec -it busybox-pod sh

# Use helm to deploy a chart
helm install --name=mychart-1.0 .\mychart\
# If the release already exists, purge it first
helm delete --purge mychart-1.0
# Use upgrade from now on
helm upgrade mychart-1.0 .\mychart\ --install
```

### Create a cronjob
```bash

```

### Create a deployment
- Definiton in yaml file `deployment-nginx.yaml`
- Enable `deploymentNginx` in `values.yaml`
```bash
helm upgrade mychart-1.0 .\mychart\
```

### Use busybox to access web server pods
- Two pods running nginx containers are now running. We can access them through their IPs.
```bash
kubectl get pods -o wide
kubectl exec -it busybox-pod sh
# or directly execute the command, passing it into container
kubectl exec -it busybox-pod -- wget <POD_IP>:<PORT>
# Access the web server
wget <POD_IP>:<PORT> # Example: wget 10.1.75.23:80
ls
cat index.html
```
- Open new terminal window to observe pods.
```
kubectl get pods -o wide -w
```
- In first terminal
```
kubectl delete pod <POD_NAME>
```

- If a pod dies it is recreated with a different IP.
- Enable `serviceNginx` in `values.yaml`
```bash
helm upgrade mychart-1.0 .\mychart\
# Observe the endpoints
kubectl get services -o wide # See `SELECTOR`
kubectl describe service my-nginx-service # See `Endpoints`
kubectl exec -it busybox-pod sh
wget <CLUSTER_IP>:<PORT> # Example: wget 10.104.111.204:80
```

### Access from web-browser
- Enable `serviceTodoApp` and `deploymentTodoApp` in `values.yaml`
```bash
# Observe EXTERNAL-IP
helm upgrade mychart-1.0 .\mychart\
```
- Open localhost in browser.

### Scale a deployment
- Scale to zero pods, try to access, scale back up.
```bash
kubectl scale deployment my-nginx-deployment --replicas=0
kubectl scale deployment my-nginx-deployment --replicas=4
# Introduce a horizontal-pod-autoscaler
kubectl autoscale deployment my-nginx-deployment --min=2 --max=6
```











### Enter busybox container on pod
```bash
kubectl run -i --tty --rm debug --image=busybox --restart=Never -- sh // Spins up a busybox and enters command prompt.
kubectl -it exec <POD_NAME> sh // If a pod is already running busybox, go into it.
```

### 
```bash
helm create <CHART_NAME>
helm template <CHART_NAME>

// Similar to template
helm install --dry-run --debug <CHART_NAME>

helm install --name nginx . --set service.type=NodePort
helm install --name nginx . --set service.type=NodePort

helm upgrade nginx ./<CHART_NAME> --set service.type=NodePort

kubectl get pods
kubectl describe pod <POD_NAME>

// 1. Get the application URL by running these commands:
export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services example-mychart)
export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
echo http://$NODE_IP:$NODE_PORT/


kubectl get services // For Cluster IP and PORT
wget <CLUSTER-IP>:<PORT> // Get nginx index.html
cat index.html
```

```
// When we don't use helm. Deploying objects directly from .yaml files
helm template --name=testname .\mychart\ -x templates\service.yaml | Out-File service-manual.yaml
kubectl apply -f .\service-manual.yaml
kubectl get services
```