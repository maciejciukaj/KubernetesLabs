# Lab 3

1. Start by creating a yaml configuration file (sidecar-pod.yaml)

2. Then use the apply command to create/update the pod

```console
kubectl apply -f sidecar-pod.yaml
```

3. Create a port forwarding connection for nginx
```console
kubectl port-forward -n lab3 sidecar-pod 8080:80
```

4. Check the content of the data.log file on localhost:8080
```console
curl http://localhost:8080/date.log
```
