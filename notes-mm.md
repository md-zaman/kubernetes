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

(vii) **Kube-proxy** Application running on the worker nodes need to communicate to each other like if you are running a webserver running in a container in a node and a database containers on another container on another node. Communications are enabled by the Kube-proxy. It ensure that the necessary rules are in place on the worker node to allow the containers running on them to reach each other.

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
    The default client which come with etcd is etcd control client. It is a command line client for etcd. You can use it to store and retrieve and store key-value pairs. To store any key-value pair:
    
```ssh
    ./etcdctl set key1 value1
```

To retrieve the stored data, run:
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
To find out the version:
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

Every information that we see when we run the etcd get command is from the etcd server
Everything that we do like adding additional nodes, pods etc is updated in etcd serer
Only after it is updated in the etcd server the change is considered complete 

There two types of kuberenetes deployment shown in this course deployment from scratch and another using the kubeadm tool.
Cluster from scratch
If you setup your cluster from scratch then you have to deploy etcd and you have to download the binaries yourself. installing the binaries and configuring etcd as a service in your master node.
There many options passed to the service a number of them related to certificates.
another one is:
--advertise-client-urls https://${INTERNAL_IP}:2379 \\
This is the address on which the etcd listens. It happens to be on the IP of the server and on the port 2379- the default port where etcd listens.
This is the URL that should be configured on the kube apiserver when it tries to reach the etcd server.

Cluster using kubeadm
The kubeadm deploys the etcd server as a pod in the kubesystem namespace:
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

To set the right version of API set the environment variable ETCDCTL_API command

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

In this case the API-Server create a pod object without assigning it to any node, updates the information in the etcd server, updates the user that the pod has been created.
The scheduler continuously monitors the api-server and realises that there's a new pod with no node assigned, the scheduler identifies the right node to place the new pod on and communicates back to the kube-apiserver, the api-server then updates the information in the etcd cluster, the api-server then passes that information to the kubelet in the appropriate worker node, the kubelet then creates the pod on the node and instructs the container runtime engine to deploy the application runtime image once done the kubelet updates the status back to the api-server and the api server then updates the data back in the etcd cluster. 
A similar pattern is followed everytime the change is requested.
The kube-apiserver is at the centre of all the different tasks that needs to be performed to make a change in the cluster. 
To summarise, the kube-apiserver is responsible for authenticating and validating request, retrieving and updating data in the etcd data store. Infact kube-apiserver is the only component that interacts with the etcd data store. the other components uses the api-server to perform the updates in the cluster in their respective areas.
If you bootstraped your your cluster using kubeadmin tool then you don't need to know this but if you're setting up this hardway then the kube-apiserver is availble as a binary in the kubernetes release page. Download and run it as a service in your kubernetes master node. 

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
ETCD servers - The option etcd servers is where you specify the loaction of the etcd servers   --etcd-servers=https://127.0.0.1:2379 \\

To view api-server option in existing cluster:
If Kubeadm
the kubeadm deploys the kube-apiserver as a pod in the kube-system namespace on the master node you can see thatby :
cat /et/kubernetes/manifests/kube-apiserver.yaml
In a non-kubeadm setup:
cat /etc/systemd/system/kube-apiserver.service

You can also see the running process and the affective option by listing the process on the master node by searching the kube-apiserver

17. Kube Controller Manager

Manages various controllers in kubernetes
In kubernetes is a process that continuously monitors the state of various components within the system and works towards bringing the whole system to the desired functioning state. 
Example:
- Deployment Controller
- CronJob
- Service Account Controller
- Node Controller
- Namespace Controller
- Job Controller
- Stateful-Set
- Daemon-Set
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

View controller-manager options (Non-kube adm setup)

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


Zaman
: this is my name
