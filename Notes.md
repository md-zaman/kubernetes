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

Day 2: Kubernetes Architecture:

9. On a high level k8s is divided in two parts: Control plane and the data plane. 
Under Control plane we have: API Server, Controler Manager, ETCD, Scheduler and Cloud Controler Manager
Under Data Plane we have: Kubelet, kube proxy and container runtime.

| **Control Plane**          | **Data Plane**    |
| --------                    | -------           |
| API Server                  | Kubelet           |
| Controler Manager           | Kube proxy        |
| Scheduler                   | Container Runtime |
| ETCD                        |                   |
| Cloud Control Manager (CCM) |                   |

Worker Node:
10. Kubelet is responsible for maintaing the pod. It ensure that the pod is always running of the pod is not running, it will inform the API server in control plane.

11. Container runtime: It can be docker (dockershim) or any other container runtime of crio, containerd or any other container runtimes which implements conatiner interface.

12. Kube proxy: provides you networking. Every container has to be allocated with IP address and load balancing capabilities. (Basically uses the IP tables on your Linux machine).

Control Plane:

13. API Server: component which exposes to the outside world. Takes request from external world. It decides on which node the pod has to be created.
14. Scheduler (Kube-scheduler): It scheduler schedules your pods/resources in k8s. 
15. ETCD : Backup store. it is key value store. Store the entire cluster information. 
16. Controller manager: K8s basically has some controllers like 'replica sets'. It ensures that such components are always running. 
17. CCM (Cloud Controller Manager): When we use our k8s cluster on eks or aks or gke and want to lets say create a pod, k8s doesn't understad the language of these cloud provider so the ccm convert this instruction to eks to talk to the api of eks. the ccm is open source and suppose if i make a cloud by the name "Zaman cloud" I can create my own ccm which can be used in k8s. If we are deploying the cluseter in on-premise ccm is not required.

18. Kubernetes has custom resources and custom resource definations for the features it doesn't have.

19. Pod is described of how to run a container. It is like a wrapper of container. In K8s instead of deploying a container we deploy a pod.
20. Why do we deploy a pod instead because k8s is an enterprise level platform and it wants to build a declarative capabilities.

21. We put 1 or more containers in a single pod (sidecar container or init containers (recheck this)) so that k8s can ensure they have advantages like allow you shared networking, shared storage, can talk to each other using local host like share the file.

22. IP address in not generated for contaier but pods.
23. kube-proxy generates the cluster ip address for ppods.
24. kubectl is the k8s cli.
25. Local k8s clusters minikube, k3s, kind, microk8s
26. Minikube create only one cluster and has a single node.

27. How to deploy a pod:
    a. Create a yml file like below:
    
    apiVersion: v1
    kind: Pod
    metadata:
    name: nginx
    spec:
    containers:
    - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

    Save the yml file.
    To create the pod use the command:

```ssh
    kubectl create pod -f pod.yml
    - creates a pod 

    kubectl get pods
    - displays all the running containers

    kubectl get pods -o wide
    - displays the details of the pod

```
27. To login to your kubernetes cluster:
    minikube ssh


Auto Scaling and Auto healing:

27. You have to use deployment to get auto-healing and auto-scaling

28. In actual scenarios we don't deploy a pod but we deploy a deployments or stateful sets or deamon sets.

29. To check the logs of the pod:
    kubectl logs nginx
    - displays the logs of the pod

30. kubectl describe nginx
    - displays all the details of the pod

31. 