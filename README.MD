# GUIA PARA PRACTICA #1 - DOCKER Y REGISTRY

## Prerequisitos

- Crear Cuenta Docker Hub: https://hub.docker.com/signup
- Instalar Docker : https://docs.docker.com/desktop/install/windows-install/

***nota: puede usar https://labs.play-with-docker.com/ para probar la construcción de  su imagen de docker y subirla a docker hub***

*Para entrar por ssh a PWD debe usar gitbash y usar el siguiente comando*
```sh
ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa
```
Despues: 
```sh
 ssh <instance_ip_with_dashes>-<short_session_id>@pwdhost
```
## pasos:

### 1 - Construir el dockerfile con ayuda del instructor.

Ejemplo de dockerfile para app en node: 
```sh
FROM node:16

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm ci --only=production

# Bundle app source
COPY . .

EXPOSE 8080
CMD [ "node", "server.js" ]
```

### 2 - Construir la  imagen usanod el comando build
```sh
docker build . -t <your username>/<my app name>
```

### 3 - Probar la imagen creando un contenedor
```sh
docker run -p 80:80 <your username>/<my app name>
```

### 4 - Loguearse en docker hub
```sh
docker login
```

### 5 - Subir la imagen de docker a docker hub
```sh
docker push <your username>/<my app name>
```

# GUIA PARA PRACTICA #2 - Despliegue y administración con KUBERNETES

## Prerequisitos

- Tener instalado minikube: https://minikube.sigs.k8s.io/docs/start/
- Instalar kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/

- Instalar Helm: https://helm.sh/docs/intro/install/

- En caso de tener una cuenta de AWS debe tener instalado el cli de AWS: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

- Se recomienda el uso de la herramienta EKSCTL: https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html

## pasos:

### 1 - Configurar accesos al cluster de kubernetes.

#### minikube: 

```sh
minikube start
```
#### AWS: 
```sh
aws eks --region us-east-1 update-kubeconfig --name <cluster_name>
```


### 2 - Crear mi propio namespace.
```sh
kubectl create ns <mi_namespace>
```
**Nota: Puede usar los archivos de ejemplo que acompañan este repositorio**
### 3 - Crear mi primer deployment.

```sh
kubectl apply -f <mi_archivo_de_deployment>
```
### 4 - Crear mi primer servicio.

```sh
kubectl apply -f <mi_archivo_de_servicio>
```

### 5 - Instalar el chart de helm para el ingresscontroller

#### minikube: 

```sh
minikube addon enable ingress
```
#### AWS: 
```sh
helm repo add stable https://charts.helm.sh/stable
helm repo update
helm repo add eks https://aws.github.io/eks-charts
helm install  eks/aws-load-balancer-controller --generate-name   --set autoDiscoverAwsRegion=true --set autoDiscoverAwsVpcID=true --set clusterName=<cluster name> --namespace <namespace>
```
### 6 - Configurar y desplegar el ingress
```sh
kubectl apply -f <mi_archivo_de_ingress>
```

### 7 - Instalar el chart de helm para el metricserver

#### minikube: 

```sh
minikube addon enable metrics-server
```
#### AWS: 
```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install metric-server bitnami/metrics-server
helm upgrade --namespace default metric-server bitnami/metrics-server --set apiService.create=true
```

### 8 - Instalar el HPA.

```sh
kubectl apply -f <mi_archivo_de_HPA>
```

### 9 - probar el servicio en tu navegador!!.