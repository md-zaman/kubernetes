Day 1:

Basics

Docker is a container platform
Kubernetes is an orchestration platform

Containers are ephemeral in nature. Means containers can die and revive anytime.

Features:
 a. Auto-healing behaviour
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

3. Kubernetes is installed as a cluster

4. Problems with Docker:
    a. Single Host - Because there only one host the containers inside it is getting impacted because of that and one of the containers in getting killed.
    b. Auto healing
    c. 

4. Docker has one more problem, it has a single host, so if there is a shortage of memory one container is killed by another container. This is not the case with k8s. k8s has multi nodes and if a container is killed by another container k8s will put that container is a different node.

5. K8s can put a faulty pod/application in a different node.

6. Kubernetes' Auto scaling : It has something called 'replication controler' or 'replica set' (Replication controler is the old name). So, you can go to the deployment.yaml file and say increase my load from 1 to 10 because my load has increased. It also supports HPA (horizontal pod scaler) using which you can directly say that whenever there is a load increase the number of containers. Whenever my container is reaching the load of 80% increase a pod.

7. Auto healing : k8s ccontrols and fix the damage. mostly control. suppose a container is going down. Even before a container goes down starts a new cotainer. Whenever API server receives a signal that a container is going down immediately it will rollout a new container/pod.

8. Enterprise nature : custom resourses- adv load balancing. k8s advancing every day by cncf. 

Day 2: Kubernetes Architecture:

9. On a high level k8s is divided in two parts: Control plane and the data plane. 
Under Control plane we have: API Server, Controler Manager, ETCD, Scheduler and Cloud Controler Manager
Under Data Plane we have: Kubelet, kube proxy and container runtime.

| **Control Plane**           | **Data Plane**    |
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

28. In actual scenarios we don't deploy a pod but we deploy a deployments or stateful sets or deamon sets. We will discuss about these things later.

29. To check the logs of the pod:
    kubectl logs nginx
    - displays the logs of the pod

30. kubectl describe nginx
    - displays all the details of the pod
    - this is how to debug

31. How to rembeber the command "kubernetes cheatsheet"

32. kubectl delete pod nginx
    - deletes the pod

33. To debug pods or apps. You 2 go-to commands could be:
    kubectl describe pod <pod_name>
    kubectl logs nginx

Day 34: Kubernetes Deployment

34. difference between container, pod and deployment

| **Container**                   | **Pod**                                 | **Deployment** |
| --------                        | -------                                 |
| Can be from docker, containerd  | Single or multiple container            | Used because auto-healing and auto scaling
| docker run -it -p -v -net       | You write a YAML. Pod is a running spec |
| Scheduler                       | Cannot do auto healing and scaling      | can do | 0 downtime
|                                 | A Pod can have single or multiple       | creates intermediate resource call replica set and then rs create pod
|                                 | container                               |

35. What k8s say is don't create a pod directly instead create a deployment. Deployment creates a replicaset (it is a k8s controller) then the rs creates a pod

36. Replicaset controller: It is a controller created by deployment. It will ensure to create no. of pod which you have mentioned in the deployemnt yaml manifest. If you say replica count is 2 it ensure that the cluster has 2 replicas of pod. So that many user can use simultanenously. This is how 0 downtime is achieved.

37. Replicaset defination from Google gemini:  

38. Controller: are someting which ensure that the desired state and actual state are same. there are default controllers and custom controllers. it is a go language application whcih k8s has written which ensures.

39. Controllers defination:

40. deployment vs replicaset: 

41. kubectl get pods -w
    - watch the container log realtime (live)

42. if you delete a pod or if a pod is deleted for any reason while you have deployed a deployment. The replicaset which was created by the deployemnt will ensure that another pod is create even before the pod is deleted. This ensures 0 downtime.


Day 35

Kubernetes Services

43. What is an ideal pod size - it depends upon the number of concurrent users. and it depends upon the number of request 1 replica of your application can handle.
43. What if there is not service in k8s?/ what if there is not component as service in k8s?
    We know containers are ephimeral in nature and that's why we deploy a deployment to ensure a particular no. of pods are running at all times.
    So, when a pod dies for any reason a new pod will come up because we have deployed a deployment (and we know to maintain the required number of services replicaset controller will start another pod). The new pod will have new IP address. Since in our case service doesn't exists in k8s, the user will send the request to the died container- which doesn't exists. So, we service acts as a load balancer where whenever there is a traffic from a user, the user is redirected to the new pod and not the previous pod. Services are mapped with pods in deployments using labels and selectors and not IP addresses.
    Features of service: 
    A. Service acts as load-balancing
    B. Service discovery - Using Labels and Selectors. For all pods that is created a label is added. This labels will be common for all pods. Service will not bother about the IP addresses but it will look for labels. The label will be same because Replicaset controller will deploy a new pod with the same YAML.
    C. Expose to external world - There are three types:
        i.   Cluster IP
        ii.  NodePort
        iii. Load balancer
        iv. There are more but these are the default types

        i. Cluster IP mode: Application will be accessible inside the k8s cluster. Here you will only get 2 benefits- Discovery and load balancing.
        ii. NodePort- This will allow your application to be accessed inside your organisation. within your org or network. They might not have access to your cluster but they have the access to your app.- the workernode ip addresses
        iii. Load balancer - In this mode service will expose your application to the external world. Suppose you have deployed you app on eks cluster. In this case you will get Elastic Load balancer IP for your service and now whoever want to access they can use this public IP address. Ths type is possible in cloud providers only as of now.
        Use Cases:
        Load Balancer - Amazon.com
        NodePort - VPC Nodes
        ClusterIP - Cluster Network
        
Kubernetes Service Deep Dive using Kubeshark
44. Activity: 

    a. minikube status
        - checks the minikube status- the running cluster

    b. We will enter the following commands to clear the kubernetes cluster

    c. kubectl get all
        - lists all the resources in current namespace in k8s cluster

    d. kubectl delete deploy <deployment_name>
        - deletes the mentioned deployment

    e. kube delete svc <service_name>
        - delete the mentioned services
            
    f. Ensure that you don't remove the default kubernetes service by the name - "service/kubernetes".

    g. Ensure that when you do "kubectl get all" you get only the "service/kubernetes" running.

    h. Let's clone a particular GitHub repository with which we will work
        git clone https://github.com/iam-veeramalla/Docker-Zero-to-Hero.git

    i. Inside the we have these files and directorries:
        zaman@MdZamanLaptop:~/k8s/Docker-Zero-to-Hero/examples/python-web-app$ ls
        Dockerfile  devops  requirements.txt
        zaman@MdZamanLaptop:~/k8s/Docker-Zero-to-Hero/examples/python-web-app$ vim Dockerfile
    j. The Dockerfile that we have has this content:
        FROM ubuntu

        WORKDIR /app

        COPY requirements.txt /app
        COPY devops /app

        RUN apt-get update && \
            apt-get install -y python3 python3-pip && \
            pip install -r requirements.txt && \
            cd devops

        ENTRYPOINT ["python3"]
        CMD ["manage.py", "runserver", "0.0.0.0:8000"]

    k. Let's build the image:
        docker build -t zamanf5/python-sample-app-demo:v1 .

    l. Now, let us make the deployment.yaml manifest
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: sample-python-app
        labels:
            app: sample-python-app
        spec:
        replicas: 2
        selector:
            matchLabels:
            app: sample-python-app
        template:
            metadata:
            labels:
                app: sample-python-app
            spec:
            containers:
            - name: python-app
                image: zamanf5/python-sample-app-demo:v1
                ports:
                - containerPort: 8000
    
    m. Now deploy the deployment:
        kubectl apply -f deployment.yaml

    n. Let's check:
        kubectl get deploy
        - shows the deployements
        check the pods also to recheck:
        kubectl get pods
        - shows the pods that are running
        To get the pod's IP address also:
        kubectl get pods -o wide
        - shows the IP address of the pods
        kubectl get pods -v=7
        - displays the pods in verbose - shows your more details
        kubectl get pods -v=9
        - this is the maximum verbosity level
        
    o. If you want to go to the application in minikube:
        minikube ssh
        The:
        curl -L http://172.17.0.5/8000/demo
        - We're are using '-L' because abhishek veeramalla said that the application he has written, it requires a redirect.
        - Here we are trying to access the app. The app runs on the port 8000/demo
        - '/demo' is context root of the application
                  
    p. If you try the same command after coming out of the k8s cluster, you won't be able to access it. This is because a pod be default will have cluster network attached to it. But you can have internal as well as external cutomers.

    q. To ensure that your application can be accessed by your organisation or the outside world we have services and as discussed we have 3 types of services:
    i. ClusterIP
    ii. NodePort Mode
    iii. Load Balancer Mode

    r. vi service.yaml
        
        apiVersion: v1
        kind: Service
        metadata:
        name: python-django-sample-app
        spec:
        type: NodePort
        selector:
            app: sample-python-app 
        ports:
            - port: 80
            targetPort: 8000
            nodePort: 30007

        Now, simply apply:
        kubectl apply -f service.yml

        ------
        Comment:
        - Esure that the correct selector is entered
        - Ensure that the correct 'targetPort' is entered. Target port is the port where your application is running. Since in this case the application is running on '8000', we have entered the same.
        ------

        To check:
        kubectl get svc
        - desplays the services

        kubectl get svc -v=9
        - shows a verbose status of the services

    s. Irrespective of what kind of service you choose whether it be ClusterIp mode, NodePort mode or Load Balancing mode, 'ClusterIp' will always be there.
    Apart from this you will also get a port mapping- the Nope IP Address. This will be under the column 'PORT(S)'. You will get this port mapping and you can access you application using this port when you are using NodePort or LoadBalancing mode. (In NodePort we don't get this because in NodePort you only have your application avaiable in the Cluster.)
    
    t. You can access the application either by ClusterIP address which you will find under the 'CLUSTERIP' column (but you have to ensure that you are loggedin in the cluster) or you can use the Nope IP address. So, suppose your Node IP Address which is getting displayed under PORT(S) is '80:30007' and here is how you can do it:
        i. Get the IP Address. In minikube we can enter the command:
            minikube ip
            Output: 192.168.64.10

            Now, you can curl and see your application:
            curl -L http://192.168.64.10:30007/demo

            We can also enter this link in the address bar of the browser and access our application.
            However, if you take the same URL and try to access it from elsewhere you will not be able to access it. This is becaue you haven't exposed your application to the outside world.

    u. To expose this existing application to the outside world we can simply edit the svc:
        kubectl edit svc python-django-sample-app
        - opens services' yaml where you can edit the service.
        - if you want to check the name of the service you can simply enter:
            kubectl get svc
            - lists all the service
    Here you can have to change the service 'type:' from 'NodePort' to 'LoadBalancer'

    v. 








    



