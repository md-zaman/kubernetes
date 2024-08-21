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

Kubernetes cluster consists of a set of nodes which can be which may be physical or virtual. It can be on-premise or on-cloud that hosts applications in the form of containers. This relate to the cargo ship in this analogy.
The `Worker Nodes` are the ships that can load containers.
But somebody needs to load the containers on the ships and not just load but plan to load, identify the right ships, store information about the ships, monitor or track the containers on the ships, manage the whole loading process, etc. This is done by the control ships. The control ships are the `Master nodes` in the Kubernetes cluster

The Master Nodes does all of these using a set of components called the `Control Plane Components`. Let's look at all of these components now: \
(i) There are many containers which are getting loaded and unloaded on a daily basis and so to maintain the information about the different ships, what container is on which ship ans what time it was loaded etc., 
All of these is stored in a highly available `Key-Value-Store` known as `ETCD`. \
**ETCD** is a database that stores information in a `key-value-format`

(ii) When ships arrive we load containers in them using cranes. The cranes identify containers that needs to be kept in ships, it identifies the right ship based on its size its capacity, the co. of containers already on the ship or any other conditions, such as destination of the ship, the type of containers it is allowed to carry, etc., These are Scheduler in a Kubernetes cluster \
**Kube-Scheduler** identifies the right node to place a container on based on the container's resource requirement, the worker node capacity or any other policies or contrains such as taints and tolerations, node affinity rules that are on them. 

(iii) The Operations teams handles takes care of ship handling, traffic control, etc., they deal with issues related to damages, the rules different ships take, etc, the cargo teams takes care of the containers, when a container is damaged or distroyed they make sure new containers are made. Similarly he have controllers which takes care of different areas.
Controllers-Managers - Node controllers, replication controllers, etc.,

(iv) Who manages these components at a high level
**Kube-APIServer** is the primary managemet component of kubernetes. The kube-APIServer is responsible for orchestrating all operations within the cluster. It exposes the kubernetes API which is used by external users to perform management operations on the cluster as well as the controllers to monitor the state of cluster and make necessary changes as required and by the worker nodes to communicate with the server. 

(v) **Container Runtime** everything is in container form here, so in every node there is a container runtime

(vi) **Kubelet** is an agent that runs on each node in a cluster. It listens for instructions from the kube-apiserver and deploys or destroys containers in the nodes as required. It priodically fetches status report from the kubelet to monitor the status of the nodes and containers on them. 

(vii) **Kube-proxy** Application running on the worker nodes need to communicate to each other like if you are running a webserver running in a conatiner in a node and a database containers on another conatiner on another nodes. Communications are enabled by the Kube-proxy. It ensure that the necessary rules are in place on the worker node to allow the containers running on them to reach each other.

### Docker vs ContainerD






