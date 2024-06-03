Day 1:

Basics

Docker is a container platform
Kubernetes is an orchestration platform

Containers are ephemeral in nature. Means containers can die and revive anytime.

Features:
 a. Auto-healing
 b. Auto-scaling

1. Auto Scaling
    In Docker, you can manually increase the number of containers in K8s, it can be done automatically. K8s also uses load balancing to redirect the traffic appropriately to the new containers.
    Load Balancing 

2. Why Docker is not used in enterprise:
    Docker does not provide:
    a. auto healing
    b. auto scaling
    c. api support
    d. white listing/ black listing 
    e. firewall

3. Kubernetes is instlled as a cluster
4. Docker has one more problem, it has a single host, so if there is a shortage of memory one container is killed by another container. This is not the case with k8s. k8s has multi nodes and if a container is killed by another container k8s will put that container is a different node.

5. K8s can put a faulty pod/application in a different node.

6. Kubernetes' Auto scaling : It has something called 'replication controler' or 'replica set' (Replication controler is the old name). So, you can go to the deployment.yaml file and say increase my load from 1 to 10 because my load has increased. It also supports HPA (horizontal pod scaler) using which you can directly say that whenever there is a load increase the number of containers. Whenever my container is reaching the load of 80% increase a pod.

7. Auto healing : k8s ccontrols and fix the damage. mostly control. suppose a container is going down. Even before a container goes down starts a new cotainer. Whenever API server receives a signal that a container is going down immediately it will rollout a new container/pod.

8. Enterprise nature : custom resourses- adv load balancing. k8s advancing every day by cncf. 

Kubernetes Architecture:

9. 
| **Control Plane **          | **Data Plane**    |
| --------                    | -------           |
| API Server                  | Kubelet           |
| Controler Manager           | Kube proxy        |
| Scheduler                   | Container Runtime |
| ETCD                        |                   |
| Cloud Control Manager (CCM) |                   |


