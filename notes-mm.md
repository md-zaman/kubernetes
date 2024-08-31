# Section 1: Introduction

Skipped (Introductory videos. No content)

# Section 2: Core Concepts

### 09. Core Concepts Section Introduction

Simply the Introduction

### 10. Download Presentation Deck for this section

### 11. Cluster Architecture

We will use the analogy of ships

We have two kinds of ships:

    (a) Cargo Ships 
        - which does the actual work of carrying containers accross the sea

    (b) Control Ships 
        - that are responsible for monitoring and managing the cargo ships

Kubernetes cluster consists of a set of nodes which may be physical or virtual. It can be on-premise or on-cloud that hosts applications in the form of containers. This relate to the cargo ship in this analogy.
The `Worker Nodes` are the ships that can load containers.
But somebody needs to load the containers on the ships and not just load but plan to load, identify the right ships, store information about the ships, monitor or track the containers on the ships, manage the whole loading process, etc. This is done by the control ships. The control ships are the `Master nodes` in the Kubernetes cluster

The Master Nodes does all of these using a set of components called the `Control Plane Components`. Let's look at all of these components now: \
(i) **ETCD** is a highly available `key-value-store` database that stores information in a `key-value-format`

(ii) **Kube-Scheduler** is a component of the kuberenetes control-plane. It identifies the right node to place a container on based on the container's resource requirement, the worker node capacity or any other policies or contrains such as taints and tolerations, node affinity rules that are on them. 

(iii) The Operations teams handles takes care of ship handling, traffic control, etc., they deal with issues related to damages, the route different ships take, etc, the cargo teams takes care of the containers, when a container is damaged or distroyed they make sure new containers are made. Similarly he have controllers which takes care of different areas.
Controllers-Managers - Node controllers, replication controllers, etc.,

(iv) Who manages these components at a high level
**Kube-APIServer** is the primary management component of kubernetes. The Kube-APIServer is responsible for orchestrating all operations within the cluster. It exposes the kubernetes API which is used by external users to perform management operations on the cluster as well as the controllers to monitor the state of cluster and make necessary changes as required and by the worker nodes to communicate with the server. 

(v) **Container Runtime** everything is in container form here, so in every node there is a container runtime

(vi) **Kubelet** is an agent that runs on each node in a cluster. It listens for instructions from the kube-apiserver and deploys or destroys containers in the nodes as required. It periodically fetches status report from the kubelet to monitor the status of the nodes and containers on them. 

(vii) **Kube-proxy** Application running on the worker nodes need to ==communicate== to each other like if you are running a webserver running in a container in a node and a database containers on another container on another node. Communications are enabled by the Kube-proxy. It ensure that the necessary rules are in place on the worker node to allow the containers running on them to reach each other.

### 12. Docker vs ContainerD

Skipping this one just for now


13. ETCD For Beginners

ETCD is a distributed reliable key-value store that is Simple, Secure and Fast

Installing ETCD
(a) Download the Binaries
    - curl -L https://github.com/etcd-io/etcd/release/download/.....

(b) Extract
    tar xzvf stcd-v3.3.11.....

(c) Run ETCD Service
    ./etcd


    When you start/run the ETCD, it start a service which runs on port 2379 by default you can then attach any client with the etcd service to store and retrieve any information.
    The default client which come with etcd is etcd control client. It is a command line client for etcd. You can use it to store and retrieve  key-value pairs. To store any key-value pair:
    
```ssh
    ./etcdctl set key1 value1
```

To retrieve the stored data, run :
```ssh
    ./etcdctl get key1
    Output: Value1
```

If you simply want to view more options in etcd type etcdctl without any arguement:
./etcdctl
- displays all the options


You might come accross different versions of etcd commands. So let's check the versions:
v0.1 Aug 2013
v0.5 Dec 2014
`v2.0 Feb 2015`
`v3.1 Jan 2017`
     Nov 2018 CNCF

The most significant is the change from v2 to v3. The API version changed in this. Etcd command changed as well. The set and get commands were changed.

To find out the version: \
./etcdctl --version
Output:
    etcd version 3.3.11
    API version: 2
- remember there are two versions here one for etcd and another for api


So, to change the etcdctl to work with api version 3 either set the environment variable called etcdctl_api=3 for each command. So right before you run each command
or you can export it as an environment variable for the entire session using the export command.
Also note that with v3 is a command. Version is now a command and not an option as it was with v2. So in v2 that was a option now its a command. If you now run:
./etcdctl
Output:
    VERSION:
        3.3.11
    API VERSION
        3.3
    COMMANDS:
            get     Gets the key or a range of keys
            ...     ...


Now after the v3,
The command to set the value is:
./etcdctl put key1 value1

The command to get the value is:
./etcd get key1
Output:
    key1
    value1

14. ETCD in Kubernetes

The ETCD data store stores information regarding the cluster such as:
- Nodes
- Pods
- Configs
- Secrets
- Accounts
- Roles
- Bindings
- Others

Every information that we see when we run the kubectl get command is from the etcd server
Everything that we do like adding additional nodes, pods etc is updated in etcd serer
Only after it is updated in the etcd server, the change is considered complete.

There two types of kuberenetes deployment shown in this course: 
- Deployment from scratch and 
- Using the kubeadm tool.
Cluster from scratch
If you setup your cluster from scratch then you have to deploy etcd and you have to download the binaries yourself. installing the binaries and configuring etcd as a service in your master node.
There are many options passed to the service a number of them related to certificates.
another one is:
--advertise-client-urls https://${INTERNAL_IP}:2379 \\
This is the address on which the etcd listens. It happens to be on the IP of the server and on the port 2379- the default port where etcd listens.
This is the URL that should be configured on the kube-apiserver when it tries to reach the etcd server.

Cluster using kubeadm
The kubeadm deploys the etcd server as a pod in the kube-system namespace:
kubectl get pods -n kube-system
To list all keys stored by kubernetes:
kubectl exec etcd-master -n kube-system etcdctl get / --prefix -keys-only

In a high availablility environment, you will have multiple master nodes in your cluster hence you will have multiple etcd instances spread accross master nodes. In such cases make sure that the etcd instances know about each other by setting the right parametre in the etcd service configuration. The initial cluster option is where you must specify the different instances of the etcd service. 

15. ETCD - Commands (Optional)

(Optional) Additional information about ETCDCTL Utility

ETCDCTL is the CLI tool used to interact with ETCD.

ETCDCTL can interact with ETCD Server using 2 API versions - Version 2 and Version 3.  By default its set to use Version 2. Each version has different sets of commands.

For example ETCDCTL version 2 supports the following commands:

etcdctl backup
etcdctl cluster-health
etcdctl mk
etcdctl mkdir
etcdctl set


Whereas the commands are different in version 3

etcdctl snapshot save 
etcdctl endpoint health
etcdctl get
etcdctl put

To set the right version of API, set the environment variable ETCDCTL_API command

export ETCDCTL_API=3



When API version is not set, it is assumed to be set to version 2. And version 3 commands listed above don't work. When API version is set to version 3, version 2 commands listed above don't work.



Apart from that, you must also specify path to certificate files so that ETCDCTL can authenticate to the ETCD API Server. The certificate files are available in the etcd-master at the following path. We discuss more about certificates in the security section of this course. So don't worry if this looks complex:

--cacert /etc/kubernetes/pki/etcd/ca.crt     
--cert /etc/kubernetes/pki/etcd/server.crt     
--key /etc/kubernetes/pki/etcd/server.key


So for the commands I showed in the previous video to work you must specify the ETCDCTL API version and path to certificate files. Below is the final form:



kubectl exec etcd-master -n kube-system -- sh -c "ETCDCTL_API=3 etcdctl get / --prefix --keys-only --limit=10 --cacert 
/etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt  --key /etc/kubernetes/pki/etcd/server.key" 


16. Kube-API Server

This is the primary management component in kubernetes
  
When you run a kube control command (kubectl). The kube control utility is reaching to the kube-apiserver. The kube-apiserver first authenticates the request and validates it. It then retrieves the data from the etcd cluster and responds back with the requested information. 
You don't really need the kubectl command line instead you can also invoke the api directly by sending the post request like:
 (a) Authenticate User
 (b) Validate Request
 (c) Retrieve Data
 (d) Update ETCD
 (e) Scheduler

In this case the API-Server creates a pod object without assigning it to any node, updates the information in the etcd server, updates the user that the pod has been created.
The scheduler continuously monitors the api-server and realises that there's a new pod with no node assigned, the scheduler identifies the right node to place the new pod on and communicates back to the kube-apiserver, the api-server then updates the information in the etcd cluster, the api-server then passes that information to the kubelet in the appropriate worker node, the kubelet then creates the pod on the node and instructs the container runtime engine to deploy the application runtime image once done the kubelet updates the status back to the api-server and the api-server then updates the data back in the etcd cluster. 
A similar pattern is followed everytime the change is requested.
The kube-apiserver is at the centre of all the different tasks that needs to be performed to make a change in the cluster. 
To summarise, the kube-apiserver is responsible for authenticating and validating request, retrieving and updating data in the etcd data store. Infact kube-apiserver is the only component that interacts with the etcd data store. the other components uses the api-server to perform the updates in the cluster in their respective areas.
If you bootstrapped your your cluster using kubeadm tool then you don't need to know this but if you're setting up this hardway then the kube-apiserver is available as a binary in the kubernetes release page. Download and run it as a service in your kubernetes master node. 

The kube-apiserver, just like etcd is run with a lot of parameters, as you can see below:
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-apiserver
ExecStart=/usr/local/bin/kube-apiserver \\
--advertise-address=${INTERNAL_IP} \\
--allow-privileged=true \\
--apiserver-count=3 \\
--authorization-mode=Node,RBAC \\
--bind-address=0.0.0.0 \\
--client-ca-file=/var/lib/kubernetes/ca.pem \\
--enable-admission-plugins=Initializers,NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota \\
--enable-swagger-ui=true \\
--etcd-cafile=/var/lib/kubernetes/ca.pem \\
--etcd-certfile=/var/lib/kubernetes/kubernetes.pem \\
--etcd-keyfile=/var/lib/kubernetes/kubernetes-key.pem \\
--etcd-servers=https://127.0.0.1:2379 \\
--event-ttl=1h \\
--experimental-encryption-provider-config=/var/lib/kubernetes/encryption-config.yaml \\
--kubelet-certificate-authority=/var/lib/kubernetes/ca.pem \\
--kubelet-client-certificate=/var/lib/kubernetes/kubernetes.pem \\
--kubelet-client-key=/var/lib/kubern

We will install all of these indiviadually later 

Let's look at a few of them:
Certificatates - these help in setting up connectivity between different components
ETCD servers - The option etcd servers is where you specify the location of the etcd servers   --etcd-servers=https://127.0.0.1:2379 \\

To view api-server option in existing cluster:
If Kubeadm
the kubeadm deploys the kube-apiserver as a pod in the kube-system namespace on the master node you can see that by :
cat /etc/kubernetes/manifests/kube-apiserver.yaml
In a non-kubeadm setup:
cat /etc/systemd/system/kube-apiserver.service

You can also see the running process and the affective option by listing the process on the master node by searching the kube-apiserver

17. Kube Controller Manager

Manages various controllers in kubernetes.
In kubernetes its a process that continuously monitors the state of various components within the system and works towards bringing the whole system to the desired functioning state. 
Example:
- Deployment Controller
- CronJob
- Service Account Controller
- Node Controller
- Namespace Controller
- Job Controller
- Stateful-Set
- Daemon-Set (not a controller but have traits)
- PV-Binder-Controller
- Endpoint-Controller
- PV-Protection-Controller
- Replicaset
- Replication-Controller

All of these is packaged together in a single package known as Controller manager
When you install kubernetes controller manager different controllers get installed as well.
How to view the controller manager?:

Install:
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-controller-manager

Extract it and run it as a service

```ssh
kube-controller-manager.service

ExecStart=/usr/local/bin/kube-controller-manager \\
--address=0.0.0.0 \\
--cluster-cidr=10.200.0.0/16 \\
--cluster-name=kubernetes \\
--cluster-signing-cert-file=/var/lib/kubernetes/ca.pem \\
--cluster-signing-key-file=/var/lib/kubernetes/ca-key.pem \\
--kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
--leader-elect=true \\
--root-ca-file=/var/lib/kubernetes/ca.pem \\
--service-account-private-key-file=/var/lib/kubernetes/service-account-key.pem \\
--service-cluster-ip-range=10.32.0.0/24 \\
--use-service-account-credentials=true \\
--v=2
kube-controller-manager.service
Node Monitor Period = 5s
Node Monitor Grace Period = 40s
POD Eviction Timeout = 5m
--node-monitor-period=5s
--node-monitor-grace-period=40s
--pod-eviction-timeout=5m0s
--controllers stringSlice Default: [*]


A list of controllers to enable. '*' enables all on-by-default controllers, 'foo' enables the controller 
named 'foo', '-foo' disables the controller named 'foo'.
All controllers: attachdetach, bootstrapsigner, clusterrole-aggregation, cronjob, csrapproving, 
csrcleaner, csrsigning, daemonset, deployment, disruption, endpoint, garbagecollector, 
horizontalpodautoscaling, job, namespace, nodeipam, nodelifecycle, persistentvolume-binder, 
persistentvolume-expander, podgc, pv-protection, pvc-protection, replicaset, replicationcontroller,
```

View kube-controller-manager - kubeadm

```ssh
kubectl get pods -n kube-system

NAMESPACE NAME READY STATUS RESTARTS AGE
kube-system coredns-78fcdf6894-hwrq9 1/1 Running 0 16m
kube-system coredns-78fcdf6894-rzhjr 1/1 Running 0 16m
kube-system etcd-master 1/1 Running 0 15m
kube-system kube-apiserver-master 1/1 Running 0 15m
kube-system kube-controller-manager-master 1/1 Running 0 15m
kube-system kube-proxy-lzt6f 1/1 Running 0 16m
kube-system kube-proxy-zm5qd 1/1 Running 0 16m
kube-system kube-scheduler-master 1/1 Running 0 15m
kube-system weave-net-29z42 2/2 Running 1 16m
kube-system weave-net-snmdl 2/2 Running 1 16m -
```

It deploys the manager as a pod in the kube-system namespace on the master node

View kube-controller-manager options 
- kubeadm

```ssh
cat /etc/kubernetes/manifests/kube-controller-manager.yaml
spec:
containers:
- command:
- kube-controller-manager
- --address=127.0.0.1
- --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
- --cluster-signing-key-file=/etc/kubernetes/pki/ca.key
- --controllers=*,bootstrapsigner,tokencleaner
- --kubeconfig=/etc/kubernetes/controller-manager.conf
- --leader-elect=true
- --root-ca-file=/etc/kubernetes/pki/ca.crt
- --service-account-private-key-file=/etc/kubernetes/pki/sa.key
- --use-service-account-credentials=true

```

View controller-manager options (Non-kubeadm setup)

```ssh
cat /etc/systemd/system/kube-controller-manager.service
[Service]
ExecStart=/usr/local/bin/kube-controller-manager \\
--address=0.0.0.0 \\
--cluster-cidr=10.200.0.0/16 \\
--cluster-name=kubernetes \\
--cluster-signing-cert-file=/var/lib/kubernetes/ca.pem \\
--cluster-signing-key-file=/var/lib/kubernetes/ca-key.pem \\
--kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
--leader-elect=true \\
--root-ca-file=/var/lib/kubernetes/ca.pem \\
--service-account-private-key-file=/var/lib/kubernetes/service-account-key.pem \\
--service-cluster-ip-range=10.32.0.0/24 \\
--use-service-account-credentials=true \\
--v=2
Restart=on-failure
RestartSec=5

```

You can also see the running process and the affective option by listing the process:

```ssh
ps -aux | grep kube-controller-manager
root 1994 2.7 5.1 154360 105024 ? Ssl 06:45 1:25 kube-controller-manager --
address=127.0.0.1 --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt --cluster-signing-key-file=/etc/kubernetes/pki/ca.key --controllers=*,bootstrapsigner,tokencleaner --
kubeconfig=/etc/kubernetes/controller-manager.conf --leader-elect=true --root-ca-file=/etc/kubernetes/pki/ca.crt --service-account-private-key-file=/etc/kubernetes/pki/sa.key
--use-service-account-credentials=true
```

18. Kube Scheduler

The scheduler is only responsible for deciding which pod goes on which node, it doesn't actually places the pod on the nodes- that's the job of the kubelet. The kubelet is who creates the pod on the node. The scheduler only decides which pod goes where. 
The scheduler places the pods on certain nodes based on certain criteria. You may have pods with certain resource requirements. You can have nodes in the cluster dedicated to certain applications. So, how this scheduler assign these pods? The scheduler looks at each pod and tries to find the best node for it. e.g., It has a set of cpu and memory requirement the scheduler goes for two phase to identify the best node for the pod:
Phases:
    (a) Filter Nodes 
    (b) Ranks Nodes

    (a) Filter Nodes: The scheduler tries to filter out the nodes that don't fit the profile for this pod. E.g., the nodes that don't have sufficient cpu and memory resources needed/requested for the pod. This way few nodes are filtered out so we are not left with fewer nodes where the pods can be placed.
    (b) Ranks Nodes : Suppose there are two nodes left, how does the scheduler picks one of the two? The scheduler ranks the nodes to identify the best fit for the pod. It uses a priority function to assign a score to the nodes on a scale of 0 to 10. e.g., the scheduler calculates number of resources that would be free on the nodes after placing the pod on them. The nodes which will be having more number of resources free even after putting the pod in the node will be ranked better and so that will be decided.

There are other way also how nodes are selecetd like:
- Resource Requirements and Limits
- Taints and Tolerations
- Node Selectors/Affinity

Installing the kube-scheduler

wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-scheduler

Extract it and run it as a service

kube-scheduler.service

ExecStart=/usr/local/bin/kube-scheduler \\
--config=/etc/kubernetes/config/kube-scheduler.yaml \\
--v=2

Now how do you view the kube-scheduler options?

If you have set it up with kubeadm:
The kubeadm tool deploys the kubescheduler as a pod in the kube-system namespace on the masternode.

cat /etc/kubernetes/manifests/kube-scheduler.yaml

spec:
containers:
- command:
- kube-scheduler
- --address=127.0.0.1
- --kubeconfig=/etc/kubernetes/scheduler.conf
- --leader-elect=true

You can also see the running process and affective options by listing the process on the master node and searching for kube-scheduler

ps -aux | grep kube-scheduler

root 2477 0.8 1.6 48524 34044 ? Ssl 17:31 0:08 kube-scheduler --
address=127.0.0.1 --kubeconfig=/etc/kubernetes/scheduler.conf --leader-elect=true



19. Kubelet
The kubelet registers the node on the kubernetes cluster. When it receives the intruction to load a container or a pod on a node it requests the container runtime engine which maybe docker to pull the required image to run an instance. The kubelet then continues to monitor the status of the pod and containers in it and reports to the kube-apiserver on a timely basis. 

If you use kubeadm to deploy the cluster, it doesn't automatically deploy the kubelets. Now that's the difference from other components.
You must manually install kubelet on your worker nodes. 
Download the installer, extract it and run it as a service.

How do you install a kubelet normally:

wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet

kubelet.service

ExecStart=/usr/local/bin/kubelet \\
--config=/var/lib/kubelet/kubelet-config.yaml \\
--container-runtime=remote \\
--container-runtime-endpoint=unix:///var/run/containerd/containerd.sock \\
--image-pull-progress-deadline=2m \\
--kubeconfig=/var/lib/kubelet/kubeconfig \\
--network-plugin=cni \\
--register-node=true \\
--v=2

You can view the running process and the affective options by listing the process on the worker node and searching for kubelet.

ps -aux | grep kubelet

root 2095 1.8 2.4 960676 98788 ? Ssl 02:32 0:36 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --
config=/var/lib/kubelet/config.yaml --cgroup-driver=cgroupfs --cni-bin-dir=/opt/cni/bin --cni-conf-dir=/etc/cni/net.d --network-plugin=cni
 

20. Kube-Proxy
 Within a kubernetes cluster every pod can reach every other pod. This is accomplished by deploying pod networking solution to the cluster. A pod network is an internal virtual network that spans accross all the nodes in the cluster to which all the pods are connected through this network there are able to communicate with each other. There are many solution as such. e.g., I have a web app deployed in the 1st node and a database application deployed on the 2nd. The web app reached the db app simply by using the ip of the pod but there is no guarantee that the ip of the database will always remain the same. So a better way to reach the db app is by using the service so we create the service to expose the database app accross the cluster. The web app can now access the database app using the name of the service 'db'. The service also gets an ip address assigned to it. whenever a pod tries to reach the service using its ip or name it forwards the traffic to the backend pod in this case the database. But what is this service and how does it get an ip? does this service joins the same pod network? The service cannot join the pod network because the service is not an actual thing - it's not a container like pod it doesn't have an interfaces or an actively listeing process - its a virtual component that only lives in kubernetes memory. But then we also said the service should be accessable accross the cluster from any nodes so how is that achieved? That's where kube-proxy comes in. 
 Kubeproxy is a process that run on each node in the kubernetes cluster. Its job is to look for new services and everytime a new service is created it create an appropriate rules on each node to forward traffic to those services to the backend pods. One way it does this is by ip tables rules, in this case it creates ip tables rule on each node in the cluster to forward traffic heading towards the ip of the service which is 10.96.0.12 to the ip of the actual pod which is 10.32.0.15. So that;s how kube proxy configure a service. More to come. This is high level over view

![alt text](<kube-proxy diagram-1.png>)

Installing kube-proxy

wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-proxy

kube-proxy.service

ExecStart=/usr/local/bin/kube-proxy \\
--config=/var/lib/kube-proxy/kube-proxy-config.yaml
Restart=on-failure
RestartSec=5

View kube-proxy- kubeadm
The kubeadm tool deploy kubeproxy as a pod on each node
In fact it is deployed as a daemonset so a single pod is always deployed on each node in the cluster

kubectl get pods -n kube-system

NAMESPACE NAME READY STATUS RESTARTS AGE
kube-system coredns-78fcdf6894-hwrq9 1/1 Running 0 16m
kube-system coredns-78fcdf6894-rzhjr 1/1 Running 0 16m
kube-system etcd-master 1/1 Running 0 15m
kube-system kube-apiserver-master 1/1 Running 0 15m
kube-system kube-controller-manager-master 1/1 Running 0 15m
kube-system kube-proxy-lzt6f 1/1 Running 0 16m
kube-system kube-proxy-zm5qd 1/1 Running 0 16m
kube-system kube-scheduler-master 1/1 Running 0 15m
kube-system weave-net-29z42 2/2 Running 1 16m
kube-system weave-net-snmdl 2/2 Running 1 16m -

kubectl get daemonset -n kube-system

NAME DESIRED CURRENT READY UP-TO-DATE AVAILABLE NODE SELECTOR AGE
kube-proxy 2 2 2 2 2 beta.kubernetes.io/arch=amd64 1h

21. Recap - Pods

Before we proceed we have two assumptions: there is an app is already built and dockerised and push onto a docker repository like dockerhub etc so that k8s can pull it down on dockerhub and a kubernetes cluster is setup and all the services are up and running

A pod is the smallest object you can create in kubernetes.

When the load increases, we deploy another pod in the same node. If the node is falling short of reasources and further pods cannot be deployed in that node we create another node and deploy a pod there (within which we have the container). 
Pods have 1 to 1 relation with the containers but are we restricted to have single container on a single pod? No. A single pod can have multiple containers exept for the fact that there are not multiple containers of the same kind. 
In most cases we have a helper container in a single pod. This sometimes do some supporting tast for our application such as processing our users enter data, processing a files uploaded by the user, etc., and you want these helper containers to live along side you application container in that case you can have both of these containers in the same pod so that when a new app conatiner is created a helper container is also created and when it dies the helper also dies. These 2 container can also communicate with each other with the help of the network directly by refering to each other by 'local host' since they share the same network space plus they share the same storage space as well.
PODs Again!

Kubectl

kubectl run nginx

kubectl run nginx --image nginx
 
kubectl get pods

22. Pods with YAML

A kubernetes defination contains always four top level fields/root level properties :

pod-defination.yml
 apiVersion: v1
 kind:
 metadata:

 spec:


apiVersion: this is version of the kubernetes api ersion we are using the create the pod. different for different resources. Differet types are v1, app/v1

kind: type of object like pods, replica set, deployment, service
metadata: data about the object like name, 

spec: the container or image. is a dictionary. 

Once the file is created we can use the following command to create the pod:
kubectl create -f pod-defination.yml
- create a pod

To see it:
kubectl get pods
- lists all pods


kubectl describe pod my-app-pod
- describes the pod

23. Demo - Pods with YAML

Created a pod


24. Practice Test Introduction

Q2. Create a new pod with the nginx image.

Solution: kubectl run nginx --image=nginx


Q3. How many pods are created now?

Note: We have created a few more pods. So please check again.

Solutions: kubectl get pods

Q4. What is the image used to create the new pods?

You must look at one of the new pods in detail to figure this out.

Sol: kubectl describe pod <pod_name>

Q5. Which nodes are these pods placed on?

You must look at all the pods in detail to figure this out.

Sol: Run the command kubectl describe pod newpods-<id> and look at the node field.
Alternatively run kubectl get pods -o wide and check for the node the pod is placed on.

Q6. How many `containers` are part of the pod webapp?

Note: We just created a new POD. Ignore the state of the POD for now.

Sol: use the describe command and look how many containers are there under containers

Q7. What images are used in the new webapp pod?

You must look at all the pods in detail to figure this out.

Sol: describe and find it under container section

Q8. What is the state of the container agentx in the pod webapp?

Wait for it to finish the ContainerCreating state

Sol: Same process as previous

Q9. What is the reason of the error of agentx container?

Sol: Same process but look under the events section

Q10. What does the READY column in the output of the kubectl get pods command indicate?

Sol: running containers/total (check again)

Q11. Delete the webapp Pod.


Once deleted, wait for the pod to fully terminate.

sol: kubectl delete pod <pod_name>

Q12. Create a new pod with the name redis and the image redis123.


Use a pod-definition YAML file. And yes the image name is wrong!

Sol: kubectl run redis --image=redis123


