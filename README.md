# sapiens42

## Meetings


### 22.03.2024
 * Kubernetes <-> VM Kommunikation
 * Sicherheitsmaßnahmen für Betreiber, Baustaube von Compendium ab 4.4 Kubernetes
 * NetworkPolicies
 * DevSecOps
 * Open Policy Agent

### 15.03.2024
 * ServiceMesh
 * Sicherheitsmaßnahmen für Image Builder / Entwickler

### 08.03.2024
* Examples
* Kubernetes Dashboard
* Ingress
* Helm, Kustomize
* Persistence

### 01.03.2024
* docker build
* namespace
* pod
* service
* networking

### 13.02.2024
* ionos
* first cluster
* intro

### 01.03.2024
Wunschthemen
```bash
    1.  Kubernetes-Ökosystem bekannte Terminologie
    2.  Management Stack als Kubernetes Deployment
    3.  CI/CD-Pipeline-Konfiguration
    4.  Kubernetes ist bereits eine komplexe Plattform und Laufzeitumgebung
    5.  Verschieben von Anwendung von einer Kubernetes-Umgebung in eine andere Kubernetes-Umgebung. Besonderheiten.
    6.  Konfigurationsparameter (nicht nur Helm-Charts + Code!) -> Parameter und Manifests
    7.  wie man eine Datenbank in Kubernetes bereitstellt, z. B. PostgreSQL.
    8.  Kubernetes-Tools/Prozessen -> Best Practice
    9.  eine Skalierbarkeit in Kubernetes-Deployments 
    10. Distroless-/ Scratch- Image 
    11. Docker – Image, Docker + Kubernetes
```
vna:
    - https://kubescape.io/
    - https://github.com/yannh/kubeconform
    - seccomp profiles
    - CKS Objectives

## Tutorilas
* [Learn Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
* [Kubernetes Tutorial for Beginners](https://www.getambassador.io/blog/kubernetes-tutorial-beginners-guide)
* [The Illustrated Children’s Guide to Kubernetes ](https://www.cncf.io/phippy/the-childrens-illustrated-guide-to-kubernetes/)

## Docker Image

```bash
cat <<EOF
#FROM scratch
#FROM ubuntu:22.04
FROM docker.io/library/ubuntu:22.04
#FROM quay.io/lib/ubuntu:22.04

RUN apt update; apt install nginx -y; rm -rf /var/cache/apt/archives; echo "Hello from Container!" > /var/www/html/index.nginx-debian.html

COPY index.nginx-debian.html /var/www/html/index.nginx-debian.html

CMD nginx -g "daemon off;"
EOF
docker build -t workshop:1.01 .
```

## Kubernetes

### Cluster
![Img](https://www.rackspace.com/sites/default/files/2023-06/Cluster.png)
![Img](https://www.upwork.com/mc/documents/Howkubernetesnodeswork.png)
### Namespace
![Img](https://k21academy.com/wp-content/uploads/2022/07/k8s-namespaces.png)
### Pod
![Img](https://linchpiner.github.io/images/k8s-mc-3.svg)
* [Docs](https://kubernetes.io/docs/concepts/workloads/pods/)
* [Multi-Container Pods in Kubernetes](https://linchpiner.github.io/k8s-multi-container-pods.html)
### Service
[Docs](https://kubernetes.io/docs/concepts/services-networking/service/)
![Service Types](https://opensource.com/sites/default/files/2022-05/4servicetypes_0.png)
### Deployment
![Img](https://www.rackspace.com/sites/default/files/2023-06/Deployment.png)
### Networking
[Docs](https://kubernetes.io/docs/concepts/cluster-administration/networking/)
![Img](https://kubernetes.io/docs/images/kubernetes-cluster-network.svg)
### Ingress
![Img](https://res.cloudinary.com/practicaldev/image/fetch/s--AnBNTOeV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_800/https://raw.githubusercontent.com/bcouetil/articles/main/images/kubernetes/nginx-architecture.png)
[Link](https://kubernetes.io/docs/concepts/services-networking/ingress/)
[GatewayAPI](https://kubernetes.io/docs/concepts/services-networking/gateway/)
### ReplicationController, DaemonSet, StatefulSet
![Img](https://www.devopsschool.com/blog/wp-content/uploads/2021/04/Difference-between-ReplicaSet-ReplicationController-2.png)
### Volumes
[Docs](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
![Img](https://www.devopsschool.com/blog/wp-content/uploads/2019/07/emptydir-hostpath-kubernetes-volume-1.jpg)
### NetworkPolicy
[Docs](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
![Img](https://github.com/ahmetb/kubernetes-network-policy-recipes/raw/master/img/4.gif)
[K8S Network Policies Recipes](https://github.com/ahmetb/kubernetes-network-policy-recipes/blob/master/00-create-cluster.md)


## Examples


### Hello World
[Hello World | LB Example](https://k8s.io/examples/service/load-balancer-example.yaml)

```bash
# create a namespace
k create ns loadbalancer
k ns loadbalancer

# apply the deplyoment
curl https://k8s.io/examples/service/load-balancer-example.yaml
kubectl apply -f https://k8s.io/examples/service/load-balancer-example.yaml

# expose
kubectl expose deployment hello-world --type=LoadBalancer --name=my-service --dry-run -oyaml
kubectl expose deployment hello-world --type=LoadBalancer --name=my-service

k get svc -owide

# scale down
k scale deployment hello-world --replicas 1
k get pods

curl $(kubectl get service my-service -o=jsonpath={.status.loadBalancer.ingress[0].ip}):8080

# scale down
k scale deployment hello-world --replicas 1
curl $(kubectl get service my-service -o=jsonpath={.status.loadBalancer.ingress[0].ip}):8080
```


### Guest Book
[Link](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/)


```bash
# create a namespace
k create ns guestbook
k ns guestbook

# redis-leader
kubectl apply -f https://k8s.io/examples/application/guestbook/redis-leader-deployment.yaml

# review
k get replicaset
k get pods
kubectl logs -f deployment/redis-leader


# service for redis-leader
kubectl apply -f https://k8s.io/examples/application/guestbook/redis-leader-service.yaml
kubectl get service -o wide


# redis folower
kubectl apply -f https://k8s.io/examples/application/guestbook/redis-follower-deployment.yaml
kubectl get pods
kubectl apply -f https://k8s.io/examples/application/guestbook/redis-follower-service.yaml
kubectl get service

# app deployment
kubectl apply -f https://k8s.io/examples/application/guestbook/frontend-deployment.yaml
kubectl get pods -l app=guestbook -l tier=frontend



# expose
kubectl apply -f https://k8s.io/examples/application/guestbook/frontend-service.yaml


# test with port-forward
kubectl port-forward svc/frontend 8080:80
curl localhost:8080

# expose with service type load balancer
k delete svc frontend
curl -sS -L  https://k8s.io/examples/application/guestbook/frontend-service.yaml | sed -e "s/#type/type/" | kubectl apply -f -
k get svc 

# scale
k scale deployment frontend --replicas 0
k scale deployment frontend --replicas 3


# test
curl $(kubectl get service frontend -o=jsonpath={.status.loadBalancer.ingress[0].ip})
```



## Helm


### Dashboard
[Link](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

```bash
kubectl apply  -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
kubectl delete  -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml


helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard


# review
curl https://github.com/kubernetes/dashboard/blob/master/charts/kubernetes-dashboard/values.yaml

```


### Ingress Controller

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm repo list
helm install nginx-ingress ingress-nginx/ingress-nginx --create-namespace --namespace nginx-ingress --set controller.publishService.enabled=true

# NOTE: configure DNS record
# NOTE: add - --enable-ssl-passthrough to the deployment
# NOTE: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx

```


### Access to Dashboard via Ingress

```bash
# create an ingerss object
{
cat <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: kubernetes-dashboard
  name: kubernetes-dashboard-web
  annotations:
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  ingressClassName: nginx
  rules:
  - host: "dashboard.sapiens.sckt.net"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: kubernetes-dashboard-kong-proxy
            port:
              number: 443
EOF
} | kubectl apply -f -

# get access token to the dashboard
{
cat <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: v1
kind: Secret
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: "admin-user"   
type: kubernetes.io/service-account-token  
EOF
}  | kubectl apply -f -
kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d
```


### Access to GuestBook via Ingress
```bash
{
cat <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: guestbook
  name: guestbook
spec:
  ingressClassName: nginx
  rules:
  - host: "guestbook.sapiens.sckt.net"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: frontend
            port:
              number: 80
EOF
} | kubectl apply -f -
```



### Stateful Application / Wordpress mit MySQL und Persistent Volumes
[Link](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/)


```bash

# prepare yaml files
curl -LO https://k8s.io/examples/application/wordpress/mysql-deployment.yaml
curl -LO https://k8s.io/examples/application/wordpress/wordpress-deployment.yaml

cat <<EOF >./kustomization.yaml
secretGenerator:
- name: mysql-pass
  literals:
  - password=YOUR_PASSWORD
EOF

cat <<EOF >>./kustomization.yaml
resources:
  - mysql-deployment.yaml
  - wordpress-deployment.yaml
EOF

# crate a new namespace
k create ns wordpress
k ns wordpress

# apply
kubectl appply -k .

{
cat <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: wordpress
  name: wordpress
spec:
  ingressClassName: nginx
  rules:
  - host: "wordpress.sapiens.sckt.net"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: wordpress
            port:
              number: 80
EOF
} | kubectl apply -f -

```



## ServiceMesh
* [Übersicht](https://servicemesh.es/)
* [Istio](https://istio.io/latest/about/service-mesh/)
* [Linkerd](https://linkerd.io/2.15/getting-started/)
* [Linkerd Architecture](https://linkerd.io/2.15/reference/architecture/)

```bash
# install linkerd binary
curl --proto '=https' --tlsv1.2 -sSfL https://run.linkerd.io/install-edge | sh
export PATH=$HOME/.linkerd2/bin:$PATH

# check version
linkerd version

# check prerequisites
linkerd check --pre
# install crds
linkerd install --crds | kubectl apply -f -
# install linkerd
linkerd install | kubectl apply -f -
# check installation
linkerd check
# deploy emojivoto app
curl --proto '=https' --tlsv1.2 -sSfL https://run.linkerd.io/emojivoto.yml   | kubectl apply -f -


# Add Ingress
{
cat <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: emojivoto
  name: emojivoto-web
spec:
  ingressClassName: nginx
  rules:
  - host: "emojivoto.sapiens.sckt.net"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: web-svc
            port:
              number: 80
EOF
} | kubectl apply -f -


# verify changes
diff -y <(kubectl get -n emojivoto deploy -o yaml ) <(kubectl get -n emojivoto deploy -o yaml | linkerd inject -)

# redeploy with linkerd
kubectl get -n emojivoto deploy -o yaml \
  | linkerd inject - \
  | kubectl apply -f -

# install viz extention (metrics)
linkerd viz install | kubectl apply -f -
# installation check
linkerd check

# viz-dashboard
linkerd viz install | kubectl apply -f -
linkerd viz dashboard
```

## Kubernetes <-> VM Kommunikation
* K8S Cluster - Network Overview (K8S Networks, VM within/without K8S Network)
* Access to Apps in K8S Cluster: (ServiceType LoadBalancer, Ingress/GatewayAPI, NodePort)
* Routing / Fireall
* ServiceMesh
* Network Policies
### Service Type LoadBalancer
* Vorteile: beliebiges Protokoll
* Nachteile: pro Service wird eine externe IP vergeben
![Image](images/service-type-loadbalancer.drawio.svg)

### Ingress / Gateway API
* Vorteile: für alle Services wird reichte eine IP
* Nachteile: eingeschränkte Protokolle (HTTP/HTTPS)
![Image](images/ingress-gateway-api.drawio.svg)

### Ingress / Gateway API
* Vorteile: funktioniert rein mit Kubernetes-Mittel (ohne externe IPs / Provider Nachhilfe)
* Nachteile: *"depricated"*, zusätzliche Firewall/Routing Aufgaben
![Image](images/service-type-nodeport.drawio.svg)



## Sicherheitsmaßnahmen für Image Builder / Entwickler
* trusted base  Images
* keep image small
* Layers: RUN, COPY, and ADD create new layers
* Multi-Stage Builds
* clean-up after installation
* use .dockerignore
* specify user
* specify workdir
* distroless images
* avoid secrets or configuration data
* Sign
* no need for ssh
* build for single process/service


## Sicherheitsmaßnahmen für Betreiber
* OperatingSystem (Selinux, AppArmor, seccomp, scanner)
* Container Runtime (capabilities, RO, rootless)
* Image (How to Build), sign and validate images
* Registry (Scan Images, whitelist allowed registries)
* Kubernetes
    - API
    - ETCD
    - RBAC
    - Cluster Configuration (kubescape.io: comliance and misconfiguration)
    - Default Network Policies
    - Pod Security (restriction for capabilities, host resources)
    - Intrusion Detection (Falco)
    - Audit Logging
    - Version up-to-date
    - Pod-to-Pod communitcation encryption (mTLS)
    - Enforce Policies


## CI/CD
![Image](images/ci-cd.drawio.svg)
