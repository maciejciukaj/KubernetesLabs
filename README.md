# Lab12


### Pulling latest nginx image
```console
docker pull nginx
```


### Creating new repository
![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/18c71fe9-f56a-44d7-a028-88e70887c107)


### Tagging new image
```console
docker tag nginx maciejciukaj/lab12_repo:lab12
```

### Pushing image to my repository
```console
docker push maciejciukaj/lab12_repo:lab12
```

### New secret created
```console
kubectl create secret docker-registry mykey --docker-server=docker.io --docker-username=maciejciukaj --docker-password=MY_PASSWORD --docker-email=MY_EMAIL
```

### [Pod manifest](https://github.com/maciejciukaj/KubernetesLabs/blob/lab12/lab12.yaml)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test12
spec:
  containers:
  - name: nginx
    image: maciejciukaj/lab12_repo:lab12
  imagePullSecrets:
  - name: mykey
```

### Creating and deploying the pod 

```console
kubectl apply -f test12.yaml
```


### Veryfing running pod
![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/14b2a7d2-31fc-49c8-9675-731201d3484d)
