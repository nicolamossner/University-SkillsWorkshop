## Demo

Open console:
- Open 2nd tab, navigate to metrics chart
- Open 3rd tab, `kubectl proxy` to start dashboard

Open four more console windows (small window size) for:
- port-forward (x2)
- busybox
- kubectl get pods -w

```bash
kubectl get all

# Install chart
helm install --name=mychart .\mychart\ # or
helm upgrade mychart .\mychart\

# Access apps through services
kubectl port-forward svc/my-nginx-service 30909:80 # in a new window
kubectl port-forward svc/todo-app-service 30908:80 # in a new window

# Delete pod, watch pods
kubectl get pods -o wide -w # in a new window
kubectl delete pod <POD_NAME>

# Scale deployment manually
kubectl scale deployment todo-app-deployment --replicas=0
kubectl scale deployment todo-app-deployment --replicas=4

# Autoscaler for php website (apache webserver)
kubectl run php-apache --image=k8s.gcr.io/hpa-example --requests=cpu=200m --limits=cpu=500m --expose --port=80
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
kubectl get hpa -w
kubectl exec -it busybox-pod sh # in a new window
    while true; do wget -q -O- http://php-apache.default.svc.cluster.local; done # or
    while true; do wget -q -O- 10.108.23.99:80; done

# Monitoring & Alerting stack at ResDiary (metrics chart)
helm install --name=metrics .\charts\metrics\ -f .\charts\metrics\values.local.yaml --namepsace=metrics # or
helm upgrade metrics .\charts\metrics\ -f .\charts\metrics\values.local.yaml --namepsace=metrics
```