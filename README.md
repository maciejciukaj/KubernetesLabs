# Lab 4

#### 1. Start by creating a yaml file (namespace.yaml) responsible for creating and configuration of the lab4 namespace

```console
kubectl apply -f namespace.yaml
```

#### Solutions used
- ```kind: ResourceQuota``` - An object type, ResourceQuota defines the resource limits for the namespace.
</br>

#### 2. Create a yaml file (deployment.yaml) responsible for creating 3 pods with the given restrictions

 ```console
kubectl apply -f deployment.yaml
```

#### Solutions used
- ```apiVersion: apps/v1``` - The <em>apps</em> resource group contains objects that are related to applications, such as Deployments, ReplicaSets, and StatefulSets
- ```replicas: 3``` - Specifies that we want to have 3 replicas of application running at the same time
- ```selector``` - Defines what sub-objects should be included in this implementation
- ```matchLabels``` - Specifies which labels should be matched. In this case, we want the app label to be <em>nginx</em>
</br>

#### 3. Check the status of running pods

 ```console
kubectl get pods -n lab4
```
<p align="center">
<img src=https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/65733a45-58cc-4c8c-b3a7-7e5128f3b8db>
</p>

```console
kubectl get all -n lab4
```

<p align="center">
<img src="https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/b6bdcbc3-25de-4c5c-9058-e06df6e2934d">
</p>


