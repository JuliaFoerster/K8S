# K8S

<details>
<summary> EXERCISE 1: Create a Kubernetes cluster.
</summary>
  <br>
  <code>brew install minikube</code> <br>
  <code>minikube start --driver docker</code> <br>
  <code>minikube status</code> 
<br>

</details>

<details>
<summary> EXERCISE 2: Deploy Mysql with 3 replicas.
</summary>
  <br>
  First of all, you want to deploy the mysql database. Deploy Mysql database with 3 replicas and volumes for data persistence. To simplify the process you can use Helm for that.<br>
<br>
  
##### Execute deployment from directory exercise_2/3_bitnami_with_replia:
<code> cd exercise_2/3_bitnami_with_replia</code>

##### 1. Use YAML files to create Volume and VolumeClaim

<code>kubectl apply -f sql-replica-pv.yaml</code> <br>
<code>kubectl apply -f sql-replica-pvc.yaml</code> <br>

##### 2. Use Helm Charts to create 3 SQL Instances using peristant volumes

<code>helm repo add bitnami https://charts.bitnami.com/bitnami</code> <br>
<code>helm search repo bitnami/</code> <br>
<code>helm install mysql bitnami/mysql -f sql-replica.yaml</code> <br>

##### 3. Check if pods run as expected: 
##### Enter pod
<code> kubectl exec -it pod/mysql-primary-0 -- /bin/bash </code>

##### DB login 
<code> mysql -u dev_user -p passworddev</code>
</details>


<details>
<summary> EXERCISE 3: Deploy your Java Application with 3 replicas.
</summary>
<br>
  Deploy your Java application with 3 replicas.
With docker-compose, you were setting env_vars on server. In K8s there are own components for that, so create ConfigMap and Secret with the values and reference them in the application deployment config file.<br>

##### Execute deployment from directory k8s

##### Create docker image from Java App and push it into your DockerHub Repository. 

<code>cd docker-exercise</code><br>
<code>gradle build</code><br>

Rename generated jar file to: java-mysql-project-1.0-SNAPSHOT.jar <br>

- Create a docker image using existing Dockerfile: 
<code>docker build -t java_app .</code><br>
- Check if the image got build:
<code>docker images</code><br>
- Login to Docker Hub:
<code>docker login</code><br>
- Push Image into your Dockerhub Repository:
<code>docker push <DOCKERHUB_USERNAME>/java-app:1.0</code><br>

##### 1. Create Key (login to Registry and create Secret in K8S)

```
DOCKER_REGISTRY_SERVER=https://index.docker.io/v1/
DOCKER_USER=your docker username
DOCKER_EMAIL=your dockerhub email
DOCKER_PASSWORD= dockerhub pwd

kubectl create secret docker-registry my-registry-key \
--docker-server=<DOCKER_REGISTRY_SERVER>
--docker-username=<DOCKERHUB_USERNAME> \
--docker-password=<DOCKERHUB_PASSWORD> \
--docker-email=<DOCKERHUB_EMAIL>
```

###### 2. Execute following commands
<code>kubectl apply -f mysql-secret.yaml</code><br>
<code>kubectl apply -f mysql-configmap.yaml</code><br>
<code>kubectl apply -f deployment.yaml</code><br>
