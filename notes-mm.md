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
(i) There are many containers which are getting loaded and unloaded on a daily basis and so to maintain the information about the different ships, which container is on which ship and what time it was loaded etc., 
All of these is stored in a highly available `Key-Value-Store` known as `ETCD`. \
**ETCD** is a database that stores information in a `key-value-format`

(ii) When ships arrive we load containers in them using cranes. The cranes identify containers that needs to be kept in ships, it identifies the right ship based on its size its capacity, the no. of containers already on the ship or any other conditions, such as destination of the ship, the type of containers it is allowed to carry, etc., These are Scheduler in a Kubernetes cluster \
**Kube-Scheduler** identifies the right node to place a container on based on the container's resource requirement, the worker node capacity or any other policies or contrains such as taints and tolerations, node affinity rules that are on them. 

(iii) The Operations teams handles takes care of ship handling, traffic control, etc., they deal with issues related to damages, the rules different ships take, etc, the cargo teams takes care of the containers, when a container is damaged or distroyed they make sure new containers are made. Similarly he have controllers which takes care of different areas.
Controllers-Managers - Node controllers, replication controllers, etc.,

(iv) Who manages these components at a high level
**Kube-APIServer** is the primary managemet component of kubernetes. The kube-APIServer is responsible for orchestrating all operations within the cluster. It exposes the kubernetes API which is used by external users to perform management operations on the cluster as well as the controllers to monitor the state of cluster and make necessary changes as required and by the worker nodes to communicate with the server. 

(v) **Container Runtime** everything is in container form here, so in every node there is a container runtime

(vi) **Kubelet** is an agent that runs on each node in a cluster. It listens for instructions from the kube-apiserver and deploys or destroys containers in the nodes as required. It periodically fetches status report from the kubelet to monitor the status of the nodes and containers on them. 

(vii) **Kube-proxy** Application running on the worker nodes need to communicate to each other like if you are running a webserver running in a container in a node and a database containers on another conatiner on another nodes. Communications are enabled by the Kube-proxy. It ensure that the necessary rules are in place on the worker node to allow the containers running on them to reach each other.

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

The most significant is the change from v2 to v3. The API version changed in this. etcd command changed as well. The set and get commands were changed.
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

The two way to install etcd shown in the course are downloading from scratch and using kubeadm
If you setup your cluster from scratch then you have to deploy etcd and you have to download the binaries yourself. installing the binaries and configuring binaries as a service in your master node.
There many options passed to the service a number of them related to certificates.  



