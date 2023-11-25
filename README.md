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

![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/a63bd536-0cb5-438a-ba11-d0316fdeee48)


#### 3. Create a yaml file (worker.yaml) based on nginx image in zad5 namepsace

 ```console
kubectl apply -f worker.yaml -n zad5
```

![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/cff5c103-043e-4c5e-a33f-5f926c25868b)


#### 4. Create a yaml file (php-apache.yaml) with deployment and service

 ```console
kubectl apply -f php-apache.yaml -n zad4
```

![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/55cdeee1-c971-4629-a0ae-ef255d7f7917)


#### 5. Create a yaml file (hpa.yaml) responsible for configuring autoscaler

 ```console
kubectl apply -f hpa.yaml -n zad4
```

Maximum number of replicas that can be created is 5. Pod worker can use a maximum of 200Mi, and php-apache can use a maximum of 250Mi. To sum up: 200Mi + 5* 250Mi < 1.5Gi

![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/565a7047-368e-4583-8173-3964d384553f)


#### 6. Launch application that generates the load

I tried using ab - Apache HTTP server benchmarking tool, but I always got 100% failed requests

 ```console
ab -n 10 -c 10 -t 30 http://10.103.31.241/
```

Load was generated using a load-generator based on the busybox image:

![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/29e34e39-0456-46bc-bde2-96df20711edf)


