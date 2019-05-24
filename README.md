# DevOps

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

## Pulling a docker image
- docker image pull {image name}:{tag}

## Changine port in a docker
docker run -p {port to expose}:{port to use inside} {image name}:{tag}

## Pods
### What is a Pod?
A Pod is a group of one or more containers with shared/network and a specfication for how to run containers. For every container we deploy, we use a pod. Each pod is bascially a wrapper for a container. They are typically a one to one mapping but it is possible for a pod to contain many containers. For example, another container may be for logging. Think of each pod as a single service.  

### Pods are not visible outside of kubernetes cluster
- Pods are only accessible from inside kubernetes cluster

### Debugging in a pod
```
kubectl exec {appname}
kubectl -it exec {appname} sh
```


## Run a pod
```
kubectl apply -f {file name.yaml}
```