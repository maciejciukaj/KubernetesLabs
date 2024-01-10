# Lab 10

In order to complete the task, 2 repositories were created:

[Source Repo](https://github.com/maciejciukaj/lab10_source_repo)

[Config Repo](https://github.com/maciejciukaj/lab10_config_repo)

## Step 1

### *index.html file*  👇

```html
<!DOCTYPE html>
<html lang="pl">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Laboratorium 10</title>
  </head>
  <body>
    <h1>Laboratorium 10</h1>
    <main>
      <span><b>Author Name:</b><i>Maciej Ciukaj</i></span>
      <br/>
      <span><b>App Version:</b> 1.0.2</span>
      <p>
        <b>Dockerfile</b>
        <br/>
        <code>
          FROM nginx:alpine <br />
          WORKDIR /usr/share/nginx/html <br />
          COPY ./index.html . <br />
          EXPOSE 80 <br />
          CMD ["nginx", "-g", "daemon off;"]
        </code>
      </p>
    </main>
  </body>
</html>
```
### *Dockerfile* 👇

```Dockerfile
FROM nginx:alpine 
WORKDIR /usr/share/nginx/html
COPY ./index.html .
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### *lab10_deployment.yaml* 👇

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lab10-deployment
spec:
  replicas: 4
  selector:
    matchLabels:
      app: lab10-app
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 5
      maxUnavailable: 2
  template:
    metadata:
      labels:
        app: lab10-app
    spec:
      containers:
        - name: lab10-app
          image: maciejciukaj/lab10-app:$version_number
```

### *lab10_service.yaml* 👇

```yaml
apiVersion: v1
kind: Service
metadata:
  name: lab10-service
spec:
  type: NodePort
  ports:
    - port: 8080
      targetPort: 80
      nodePort: 30007
  selector:
    app: lab10-app
```

### *lab10_ingress.yaml*  👇

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: lab10-ingress
spec:
  rules:
    - host: zad2.lab
      http:
        paths:
          - pathType: Prefix
            path: "/"
            backend:
              service:
                name: lab10-service
                port:
                  number: 8080
```

## Step 2 

### *zad2lab10.yaml* 👇

```yaml
name: Docker

on:
  workflow_dispatch:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

jobs:
  appVersion:
    runs-on: ubuntu-latest
    outputs:
      version_number: ${{ steps.vars.outputs.version_number }}
    steps:
      - name: Check out the repo
        uses: actions/checkout@v4
      - name: set-output
        id: vars
        run: |
          version_number=$(sed -n 's/.*<b>App Version:<\/b> \(.*\)<\/span>.*/\1/p' index.html)
          echo "version_number=${version_number}" >> "$GITHUB_OUTPUT"
        shell: bash

  dockerCI:
    needs: appVersion
    runs-on: ubuntu-latest
    steps:
      - name: Check out the repo
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_HUB_LOGIN }}
          password: ${{ secrets.DOCKER_HUB_PASSWORD }}
      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: maciejciukaj/lab10-app:${{ needs.appVersion.outputs.version_number}}

  kubernetesCI:
    needs: [appVersion, dockerCI]
    runs-on: ubuntu-latest
    steps:
      - name: Check out the repo
        uses: actions/checkout@v4
        with:
          repository: maciejciukaj/lab10_config_repo
          token: ${{ secrets.ACTIONS_TOKEN }}
      - run: |
          version_number=${{ needs.appVersion.outputs.version_number }}
          sed -i 's/maciejciukaj\/lab10-app:.*/maciejciukaj\/lab10-app:$version_number/g' lab10_deployment.yaml
          git config user.name github-actions
          git config user.email github-actions@github.com
          git add -u
          git commit -m "updated app version ${{ needs.appVersion.outputs.version_number }}"
          git push
```
### Workflow completed successfully 👍

![Zrzut ekranu 2024-01-10 184009](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/3067c343-8eb9-40f4-9e0b-477ca8e59358)
[Link to workflow](https://github.com/maciejciukaj/lab10_source_repo/actions/runs/7478696014)

## Step 3

### *op-stepcd.yaml* 👇

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: stepcd
spec:
  schedule: "*/2 * * * *"
  concurrencyPolicy: Forbid
  jobTemplate:
    spec:
      backoffLimit: 0
      template:
        spec:
          restartPolicy: Never
          serviceAccountName: gitops
          containers:
            - name: zad2gitops
              image: maciejciukaj/zad2gitops
              command: [sh, -e, -c]
              args:
                - git clone https://github.com/maciejciukaj/lab10_config_repo.git /tmp/lab10_config_repo && find /tmp/lab10_config_repo -name '*.yaml' -exec kubectl apply -f {} \;
```

### *dockerfile* 👇

```dockerfile
FROM alpine:latest
  
RUN apk update && \
    apk add --no-cache git curl && \
    apk add --no-cache --repository=http://dl-cdn.alpinelinux.org/alpine/edge/community kubectl
CMD ["/bin/sh"]
```

```console
kubectl create sa gitops
kubectl create clusterrolebinding gitops-admin --clusterrole=cluster-admin --serviceaccount default:gitops
```

### Building a docker image

```console
docker build -t maciejciukaj/zad2gitops .
```

![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/dec94d8e-a80a-4d95-b7b5-49c58327b58b)


### Pushing a docker image to DockerHub

```console
docker push maciejciukaj/zad2gitops:latest
```

![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/5754ad7d-63bf-4ccd-bbb5-7a30140a068b)


### Creating and running a cronjob

```console
kubectl apply -f op-stepcd.yaml
```
![image](https://github.com/maciejciukaj/KubernetesLabs/assets/86522973/f6d7592a-b74b-4a35-a890-6b282e789154)




