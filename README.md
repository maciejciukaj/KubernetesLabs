# Lab 5

#### 1. Start by creating namespaces

```console
kubectl create namespace zad4
kubectl create namespace zad5
```

#### 2. Create a yaml file (quota.yaml) responsible for  resource limitation in zad5 namepsace

 ```console
kubectl apply -f quota.yaml -n zad5
```

#### 3. Create a yaml file (worker.yaml) based on nginx image in zad5 namepsace

 ```console
kubectl apply -f worker.yaml -n zad5
```

#### 4. Create a yaml file (php-apache.yaml) with deployment and service

 ```console
kubectl apply -f php-apache.yaml -n zad4
```

#### 5. Create a yaml file (hpa.yaml) responsible for configuring autoscaler

 ```console
kubectl apply -f hpa.yaml -n zad4
```

#### 6. Check the status of running pods

 ```console
kubectl get pods -n lab5
```

<p align="center">
<img src="https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/b6bdcbc3-25de-4c5c-9058-e06df6e2934d">
</p>


