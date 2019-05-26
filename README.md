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
- Should be stateless for replication.  

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

## Master Node
- Manages all nodes in the architecture.  
- Detects which node has failed. 

### What happens if a master node dies?
- Temporary lost of master is not a problem as other nodes can operate without it. As long as master is restored, it is fine.  
- If a pod dies when master is dead, it won't be restored until master is up again.  

### Troubleshooting a 'Delinquent' node
- Use visualisation tools.   

## kops
- `ig` instance group

### Create configuration
```
kops create secret --name ${NAME} sshpublickey admin -i ~/.ssh/id_rsa.pub
```
### Create instance
```
kops update cluster ${NAME} --yes
```
### Status check
```
kops validate cluster
```
### Delete cluster
```
kops delete cluster --name ${NAME} --yes
```

## StorageClass
A `StorageClass` provides a way for admin to describe the classes of storage they want to use. `StorageClass` is dynamically created as when they are needed.  
- Provisioner: provisions cluster.

## Logging
### Manually logging
Directly inspecting the log
```
kubectl logs -f ${NAME}
```
### Distributed logging: ELK Stack (ElasticStack)
Loggin of multiple software component. We install another software component (Logstash or Fluentd) onto a node/ec2 instance to do logging. It gather all the logging process that it can find on a node. The logging software must be installed on the same node.  
Use Elasticsearch to search through log files. Elasticsearch is a distributed search and analytic engine. We stream log file results into elasticsearch and use a visualisation tool like Kibana to see the data. 

## Alerts
### Prometheus
- Used for logging and gather data, but does have it's own frontend.  
- Monitoring load, traffic and actitivies etc.  
- AlertManager to send alert to some place. e.g slack

### Grafana
Frontend for visualisation. 

## Kubernetes secrets
### See secrets
```
kubectl get secret
```

### Create secret
```
kubectl create secret ...
```

## Requests and Limits
How much RAM, CPU and memory we think a container will need.  
Use `request` label.  
See details
```
kubectl describe node ${NAME}
```
- Can have fractions of CPU.  
See usage
```
kubectl top pod
```
- Request is not a limit and is just a suggestions.  

### Setting requests
Set based on usage from `kubectl top pod`.

## Scaling
### Horizontal Pod Autoscaling
- Kubernetes to auto scale depending on workload.  
- Not every pod can be replicated.  
- Increase number of instance.  
- Use `HPA`, Horizontal Pod Autoscaler. If the usage >50% cpu request, autoscale to maximum of N pods.  
```
kubectl autoscale deployment ${pod name} --cpu-percent {amount} --min {n} --max {n}
```
Use below with spec, status as well
```
kind: HorizontalPodAutoscaler
```
- Process of scaling up and down is CPU intensive so we want to avoid threshing. Therefore, it does not change scale immediately until it has observed consistent usage.  

#### When to replicate?
- Ensure the software inside the pod so that if it is replicated, it can still behave properly.  
- Database are hard to replicated.  

### Readiness and Liveness Probes
- In kubernetes, as soon as the pod is ready, it change its status to running. However, even when a pod is ready, it may not be actually ready since the code may not be ready.   
- We setup readiness probs so that kubernetes test if a container is ready. We can setup so it test using `httpGet` or other methods.  
- A liveness probe will run for duration of the lifetime and restart when the container has failed.  

## Scheduler
- Tells kubernetes how to distribute pods to nodes.  
- Calculates resources and allocate pods to nodes that is appropriate.  
- When a new pod added to a node that makes the current node run out of memory, one pod will be removed to another pod.  
- Scheduler evicts `BestEffort` first, then `Burstable` then `Guaranteed`.  
- Evicted pods are rescueduled to another node.    

### Pod Labels
`QoS: Guaranteed`: A pod that has good specficiation with request and limit. Scheduler doesn't worry about theses.    
`QoS: Burstable`: A pod that only specifies request. Request is just a hint. The pod is allowed to do a burst for a short period. Kubernetes scheduler is going to evict one or more pods for the node.  
`QoS: BestEffort`: A pod with no specificaiton with request and limit.  
If a pod goes over its limit, it is automatically evicted and ineligble for rescueduling.  

## Role Based Access Control (RBAC)
- Different access level to user.  
- User may only create their own pods in their own namespace etc.  
- `apiGroups` for different apis and `resources` for different access to resources like `deployment`, `pods` and `services`

### Cluster role
- Cluster wise role

### Context
- Kubernetes expect to use an external service for authentication.   
- Kubernetes only accept certificate that has been signed by kubernetes certificate authority. This is done automatically in kops. The cerrificate must be generated by kubernetes and is done by super user.   

#### Generate certificate
```
openssl genrsa -out ${file name} 2048
openssl req -new -key ${file name} -out req.csr -subj "/CN=${user name}/O=${user name}" # CN for common name
openssl x509 -req -in req.csr -CA ${file name} -CAkey {$private key name}-CAcreateserial -out ${wanted fire name} -days {number of days valid for}
```

#### Installing a certificate
```
sudo mkdir /home/username/.certs
sudo mv username.crt /home/username/.certs
sudo mv privatekey.key /home/username/.certs
sudo mv kubernetes.crt /home/username/.certs
sudo chown -R username:username /home/username/.certs
```
User need to get it into config
```
kubectl config set-credentials username --client-certificate={cert file name} --client-key={private key}
kubectl config set-cluster cluster name --certificate-authority={kubernete.crt}
```

## ConfigMap
Use `envFrom` to load config map for environment variables.  

## Ingress Controller
Load balancer talks to ingress controller which does routing decisions. Usually runs on nginx. 

## Batch jobs
A Job creates one or more Pods and ensures that a specified number of them successfully terminate. As pods successfully complete, the Job tracks the successful completions. When a specified number of successful completions is reached, the task (ie, Job) is complete. Deleting a Job will clean up the Pods it created.

## Cron jobs
Runs a job periodically. One CronJob object is like one line of a crontab (cron table) file. It runs a job periodically on a given schedule, written in Cron format.

## DaemonSet
A DaemonSet ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected. Deleting a DaemonSet will clean up the Pods it created.  
- Uncommon for microservice

## StatefulSets
- A stateful set is not used for persistence. Not for saving data.  
- Sometimes you need a set of pods with known, predictable names as we want clients to be able to call them directly. Normally, we don't do this. 
- In a `StatefulSet`, the pods will have a predictable name: 0, 1, 2.  
- Usually used for system pods.  
- Pods will always start up in sequence.  
- Usual use: replicate database.  

### Database 
Use statefulset then each part has its own name and identity.  
- In mongoDb, it launches multiple instance with one as a primary. Every insert, it goes to the primary for communication. Then the other copies get data from the primary.  

## CI/CD
### Jenkins
- Use webhooks for automatic deployment

#### Multibranch pipeline
- Picks up Jenkinsfile from multiple branches.  

#### Organisation pipeline
- Credentials. 
- Owner: organisation name. 
- Search for all jenkinsfile in entire organisation. 



