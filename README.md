# k8s_project
## preparations 
```sh
alias k="kubectl"
export NS="k8s-project"

# Create the namespace 
k create ns k8s-project

# Install the RabbitMQ in the namespace 
helm install my-rabbitmq oci://registry-1.docker.io/bitnamicharts/rabbitmq -n $NS
```

Verify Rabbitmq is up 
```shell
# check using 
k get pods/my-rabbitmq-0 -n $NS
```
![img_1.png](img_1.png)



# Project Description
![img.png](img.png)



# Steps 
## 1. Install jenkins on Minikube with Agents (Docker, helm)
See details in k8s-jenkins-project/README.MD 

## 2. Create the k8s-project directory 
```shell
cd k8s_project
mkdir k8s-project 
```

## 3. Build the helm files 
### 3.1. create a template (in k8s_project) 
```shell
helm create k8s-project 
```
### 3.2. Update the helm template
In the k8s-project directory , populate  k8s yaml files of deployment, service etc'` 
Use the images created in the CI pipeline (uploaded to github):
- yidgar11/consumer:1.0 
- yidgar11/producer:1.0 

To check the outcome of the templates files 
```shell
helm template k8s-project .
```



# Appendix 
## To check the containers status in the pod
```shell
k get pod ${PODNAME} -n ${NS} -o jsonpath='{range .status.containerStatuses[*]}{"\nContainer: "}{.name}{"\nState: "}{.state}{"\nReady: "}{.ready}{"\n"}{end}'
```

# RabbitMQ
## get the rabbitmq password (user=user)
```shell
kubectl get secret --namespace k8s-project my-rabbitmq -o jsonpath="{.data.rabbitmq-password}" | base64 -d
``` 

## To Access the RabbitMQ AMQP port:
```shell
# URL : amqp://127.0.0.1:5672/
kubectl port-forward --namespace k8s-project svc/my-rabbitmq 5672:5672
```

## To Access the RabbitMQ Management interface:
```shell
# URL : http://127.0.0.1:15672/
kubectl port-forward --namespace k8s-project svc/my-rabbitmq 15672:15672
```

