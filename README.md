# DevOps

## Commands
### Pulling a docker image
```
- docker image pull {image name}:{tag}
```

### Changine port in a docker
```
docker run -p {port to expose}:{port to use inside} {image name}:{tag}
```

### Debugging in a pod
```
kubectl exec {appname}
kubectl -it exec {appname} sh
```

### Run a pod
```
kubectl apply -f {file name.yaml}
```

### See info of service/pod/cluster
See all pods and service
```
kubectl get all
kubectl get -n  {name of namespace}
```
See all pods
```
kubectl get po  # other options to show labels etc
```
See all service
```
kubectl get service
```
See persistant volume claims
```
kubectl get pv
```
See details and events
```
kubectl describe svc/po/rs {service name}
```
See logs
```
kubectl logs -f {name}
```

### Rollout
```
kubectl rollout status deploy {name}
kubectl rollout history deploy {name}
kubectl rollout undo deploy {name} --to-revision={revision to go back to} # by default goes back by 1 version
```

## Difference between kubernetes and Docker swarm?
- Kubernetes is far richer and can do alot more.  
- Docker swarm is easier to work with.  

## What is Docker?
Container to allow software environment to be contained and run without problem.  

## Container vs Images
- Images contains all the environment/software that is required to run the software.  
- We package the dependencies into images.  
- When we run an image, the running images is called the container.  
- A container is an instance of a running image.  

## Docker CLI and Docker daemon
- Docer CLI connects to Docker daemon running on a server.  

## Docker mount
- Store Docker data outside the container.  

## Pods
### What is a Pod?
A Pod is a group of one or more containers with shared/network and a specfication for how to run containers. For every container we deploy, we use a pod. Each pod is bascially a wrapper for a container. They are typically a one to one mapping but it is possible for a pod to contain many containers. For example, another container may be for logging. Think of each pod as a single service.  

### Pods are not visible outside of kubernetes cluster
- Pods are only accessible from inside kubernetes cluster

### Pods characteristitcs
- Pods regularly dies.  
- Pods are short lived.  

### Labels 
Key value pairs. A service will look for matching key value pair to select a pod.  

## Service
- A long running object. 
- Have IP address.  
- Have stable fixed ports.  
- We attach services to pods. 
- With a service, we can connect to kubernetes cluster and a service will find a suitable port to service that request.  
- Need good important service names.  

### Types
#### NodePort
Expose a port inside kubernetes cluster. We can use nodePort to specify but have to use any number larger than 30000. Not used in production often as we use load balancer.  

#### ClusterIP
Makes the service only available inside the cluster.  

#### LoadBalancer
Available for some cloud provider only.  

## How to avoid down time between versions?
Use the release label in a pod and create a new pod with new release version label. After the pod is running, change the selector in the service.  

## ReplicaSets
- If pods used too much resource or some other reason, it may die.  
- By doing `kubectl apply -f ` we are responsible for managing the pod and there is no way of bringing up the same pod again.  
- Kubernetes does not automatically handler pod failure.  
- ReplicaSets is an extra configuration to kubernetes. We specify how many instance of the same pod we want kubernetes to run at the same time so that if one fails, kubernetes will bring up another one.

## Deployment
- Deployment is a replicaset with rolling updates. It has 0 downtime.  
- You can do roll backs with deployment.  
- When a new version of replicaset is running, the old one's replica is set to 0. Therefore, when we want to roll back, we can just change number of replicas.  

## Rollout
- Better version management

## Why we don't run multiple container in one pod?
- Hard to handle failure.  
- We want to separate them into different services in different pods.  

## Kubernetes has it's own DNS system
- Kubernetes has `kube-dns` which runs automatically in the background to manage dns.  

## Namespace
- Separate different services into different areas/packages.  

## Microservices
- Opposite of monolith. In monolith, you have one single application that contains code for many components. In microservice, the architecture is made up of many applications.  
- In monolith, we cannot upgrade components separately and we have to coordinate upgrades. However, in microservice, we can do this as the components may run on their own separate containers/hardwares.  

### Microservice design
#### Single responsibility principle
- Each service should be responsible for only one business requirement.  

#### Highly Coheisve
- Should be handling only one business requirement/one responsibility

#### Loosely Coupled 
- Not be too dependent on other service.  
- Minimize interface between services.  

#### No Integration Database
- Breaks cohesive and couple rule.  
- Each service has its own store.  
- Can have multiple type of different storage.  
- Break database tables into different services.  

### API Gateway
- Stop frontend or client side to directly touch all component of the microservices.  
- Single point of entry. It delegates calls to appropriate services.  

### Persistance
- Data storage so data is not lost when system redeployed.  

#### hostPath vs persistentVolumeClaim
- hostPath is only for local and is hard coded.  
- When we change to change to cloud, we use `persistentVolumeClaim` as we will have to change the hostPath to all different config files. `persistentVolumeClaim` is a pointer to config file. 
-`PersistentVolumeClaim` is what we want 
- `PersistentVolume` is what is implemented

#### PersistentVolumeClaim vs PersistentVolume
The storage between what we want and what is implemented need to be appropriate. E.g if we want 20Gi, the physical one need to have at least that requirement.  

#### How to give different type of storage?
Use `storageClassName`. The `PersistentVolumeClaim` will search through cluster to find one that matches the name and storage.  


