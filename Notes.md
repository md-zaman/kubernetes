Day 1:

Basics

Docker is a container platform
Kubernetes is an orchestration platform

Containers are ephemeral in nature. Means containers can die and revive anytime.

Features:
 a. Auto-healing behaviour
 b. Auto-scaling

1. Auto Scaling
    In Docker, you can manually increase the number of containers. In K8s, it can be done automatically. K8s also uses load balancing to redirect the traffic appropriately to the new containers.
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
    c. ...

4. Docker has one more problem, it has a single host, so if there is a shortage of memory one container is killed by another container. This is not the case with k8s. k8s has multi nodes and if a container is killed by another container k8s will put that container in a different node.

5. K8s can put a faulty pod/application in a different node.

6. Kubernetes' Auto scaling : It has something called 'replication controller' or 'replica set' (Replication controler is the old name). So, you can go to the deployment.yaml file and say increase the load from 1 to 10 because load has increased. It also supports HPA (horizontal pod scaler) using which you can directly say that whenever there is a increase in load, increase the number of containers. Whenever my container is reaching the load of 80% increase a pod.

7. Auto healing : k8s controls and fixes the damage. mostly control. suppose a container is going down. Even before a container goes down, k8s starts a new container. Whenever API server receives a signal that a container is going down immediately it will rollout a new container/pod.

8. Enterprise nature: custom resourses- adv load balancing. k8s advancing every day by cncf. 

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
10. Kubelet is responsible for maintaing the pod. It ensure that the pod is always running if the pod is not running, it will inform the API Server in control plane.

11. Container runtime: It can be docker (dockershim) or any other container runtime of crio, containerd or any other container runtimes which implements container interface.

12. Kube proxy: provides you networking. Every container has to be allocated with IP address and load balancing capabilities. (Basically uses the IP tables on your Linux machine).

Control Plane:

13. API Server: component which exposes to the outside world. Takes request from external world. It decides on which node the pod has to be created.
14. Scheduler (Kube-scheduler): It schedules your pods/resources in k8s. 
15. ETCD: Backup store. It is a key value store. Store the entire cluster information. 
16. Controller manager: K8s basically has some controllers like 'replica sets'. It ensures that such components are always running. 
17. CCM (Cloud Controller Manager): When we use our k8s cluster on eks or aks or gke and want to lets say create a pod, k8s doesn't understand the language of these cloud provider so the ccm convert this instruction to eks to talk to the api of eks. The CCM is open source and suppose if I make a cloud by the name "Zaman cloud" I can create my own CCM which can be used in k8s. If we are deploying the cluster in on-premise, CCM is not required.

18. Kubernetes has custom resources and custom resource definations for the features it doesn't have.

19. Pod is described of how to run a container. It is like a wrapper of container. In K8s instead of deploying a container we deploy a pod.
20. Why do we deploy a pod instead because k8s is an enterprise level platform and it wants to build a declarative capabilities.

21. We put one or more containers in a single pod (sidecar container or init containers (recheck this)) so that k8s can ensure they have advantages like allow you shared networking, shared storage, can talk to each other using local host like share the file.

22. IP address in not generated for containers but pods.
23. kube-proxy generates the cluster IP address for pods.
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

33. To debug pods or apps. Your go-to commands could be:
    kubectl describe pod <pod_name>
    kubectl logs nginx

Day 34: Kubernetes Deployment

34. Difference between Container, Pod and Deployment

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
    A ReplicaSet (RS) is a controller that ensures a specified number of identical Pods are running at any given time. It's a core component for managing stateless applications in Kubernetes.

    Key Functions:

    **Replication**: A ReplicaSet maintains the desired number of Pod replicas, ensuring your application can handle incoming traffic or workload.
    **Self-Healing**: If a Pod fails, the ReplicaSet automatically replaces it to maintain the desired replica count.
    **Scaling**: You can easily scale your application up or down by adjusting the number of replicas in the ReplicaSet.

38. Controller: are someting which ensure that the desired state and actual state are same. There are default controllers and custom controllers. It is a go language application which k8s has written which ensures this.

39. Controllers defination from Gemini:
    Controllers are the core components that continuously monitor and maintain the desired state of your cluster. They operate in control loops, constantly comparing the actual state of objects (like Pods, Deployments, Services) to their desired state, and taking actions to reconcile any differences.

    **Key Concepts**:

    **Control Loop**: Controllers operate in an endless loop, performing the following steps:

    **Observe**: Watch for changes in the state of specific Kubernetes objects.
    **Analyze**: Compare the current state to the desired state defined in the object's specification.
    **Act**: Take necessary actions to bring the current state closer to the desired state.
    **Types of Controllers**: Kubernetes has built-in controllers for managing different kinds of objects:

    **Deployment Controller**: Manages ReplicaSets to ensure the correct number of Pods are running.
    **ReplicaSet Controller**: Ensures the desired number of identical Pods are running.
    **DaemonSet Controller**: Runs a copy of a Pod on each node in the cluster.
    **Job Controller**: Creates one or more Pods to complete a task and then stops.
    **CronJob Controller**: Creates Jobs on a schedule.
    **StatefulSet Controller**: Manages stateful applications with unique network identities and persistent storage.
    **Custom Controllers**: You can create your own controllers to manage custom resources or extend the functionality of Kubernetes.

40. Deployment vs Replicaset: 
    

41. kubectl get pods -w
    - watch the container log realtime (live)

42. If you delete a pod or if a pod is deleted for any reason while you have deployed a deployment. The replicaset which was created by the deployemnt will ensure that another pod is create even before the pod is deleted. This ensures 0 downtime.


Day 35

Kubernetes Services

43. What is an ideal pod size - it depends upon the number of concurrent users. and it depends upon the number of request 1 replica of your application can handle.
43. What if there is not service in k8s?/ what if there is not component as service in k8s?
    We know containers are ephimeral in nature and that's why we deploy a deployment to ensure a particular no. of pods are running at all times.
    So, when a pod dies for any reason a new pod will come up because we have deployed a deployment (and we know to maintain the required number of services replicaset controller will start another pod). The new pod will have new IP address. Since in our case service doesn't exists in k8s, the user will send the request to the died container- which doesn't exists. So, service acts as a load balancer where whenever there is a traffic from a user, the user is redirected to the new pod and not the previous pod. Services are mapped with pods in deployments using **labels** and **selectors** and not IP addresses.
    Features of service: 
    **A. Service acts as load-balancing**
    **B. Service Discovery**- Using **Labels** and **Selectors**. 
    For all pods that is created, a `label` is added. This label will be common for all pods. Service will not bother about the IP addresses but it will look for labels. The label will be same because Replicaset controller will deploy a new pod with the same YAML.
    **C. Expose to External World** - There are three types:
        i.   Cluster IP
        ii.  NodePort
        iii. Load balancer
        iv. There are more but these are the default types

        i. Cluster IP mode: Application will be accessible inside the k8s cluster. Here you will only get 2 benefits- Discovery and load balancing.
        ii. NodePort- This will allow your application to be accessed inside your organisation. within your org or network. They might not have access to your cluster but they have the access to your app.- the workernode ip addresses
        iii. Load balancer - In this mode service will expose your application to the external world. Suppose you have deployed you app on eks cluster. In this case you will get Elastic Load balancer IP for your service and now whoever want to access they can use this public IP address. This type is possible in cloud providers only as of now.
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
        ```sh
        zaman@MdZamanLaptop:~/k8s/Docker-Zero-to-Hero/examples/python-web-app$ ls
        Dockerfile  devops  requirements.txt
        zaman@MdZamanLaptop:~/k8s/Docker-Zero-to-Hero/examples/python-web-app$ vim Dockerfile
        ```
    j. The Dockerfile that we have has this content:
        ```sh
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
        ```

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
        Then:
        curl -L http://172.17.0.5/8000/demo
        - We're are using '-L' because abhishek veeramalla said that the application he has written, it requires a redirect.
        - Here we are trying to access the app. The app runs on the port 8000/demo
        - '/demo' is context root of the application
                  
    p. If you try the same command after coming out of the k8s cluster, you won't be able to access it. This is because a pod by default will have cluster network attached to it. But you can have internal as well as external cutomers.

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
        - displays the services

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
            However, if you take the same URL and try to access it from elsewhere you will not be able to access it. This is because you haven't exposed your application to the outside world.

    u. To expose this existing application to the outside world we can simply edit the svc:
        kubectl edit svc python-django-sample-app
        - opens services' yaml where you can edit the service.
        - if you want to check the name of the service you can simply enter:
            kubectl get svc
            - lists all the service
    Here you can have to change the service 'type:' from 'NodePort' to 'LoadBalancer'
    This will not work here because we are using minikube. But suppose you're using ec2 instance you can do it by accessing the ec2 instances' public IP.
    These things are taken care of by the CCM- Cloud Control Manager.
    After you save this service aftermaking the changes and enter 
    'kubectl get svc'
    You will find that the status under the 'EXTERNAL-IP' says '<pending>' because this is minikube.
    The Cloud Controller manager generates this IP
    Extra Info: there is aproject called MetalLB where you can do this using the minikube.

    v. Service can do three things:
        a. LoadBalancing - Done
        d. Service Discovery
        c. Expose - Done

        b. Service Discovery: An activity for this
            Let us edit the svc and change the 'selector' value and see it goes:
            kubectl edit svc python-django-sample-app
            - edits the service referred

            Now, after changing the selector the labels and selectors are different. Let's find out if we can access our application with the Node IP. We can enter the command in the browser also. 'http://192.168.64.10:30007/demo'.
            Here you will find that our application is not accessible.

        a. LoadBalancing: Here he used kubeshark application to show how traffic is being redirected. You can write your own something.



Ingress

There are 2 problems which ingress addresses which Services in kubernetes were not offering:
    a. Enterprise & TLS Load Balancing
        E.g.,
        i.   Sticky sessions LB
        ii.  TLS based LB
        iii. Path Based LB
        iv.  Host based LB
        v.   Ratio based LB
    b. Charges for Load Balancing
        Cloud provider will charge you for each and every service type. for each Static IP.

As a kubernetes user you can create an ingress resource and what kubernetes told to different load balancers like:
    - Nginx
    - f5
    - Ambassador
    - Treffic
    - HA proxy
    Kubernetes told them to create ingress controllers. So, the nginx company will write the nginx ingress controller and then you can use it to your k8s cluster. You can deploy it using helm chart you can deploy using yaml manifest. 

User will write the ingress and load balancing companies will create the ingress 'controllers' and they will place their ingress controllers on github k8s page and they will provide the steps on how to install this ingress controllers using helm charts or any other way.
As for user, after you have created the ingress resource, you also have to deploy ingress controllers and a user can choose which ingress controller he wants to use.
So basically ingress is a load balancer and some times it is LB + API Gateway also.
In real life you have to deploy the Nginx controller in your k8s cluster after that you will create ingress resource depending upon the capabilities that you need so suppose if you need path based routing, you will create one type of ingress, if you need TLS based you will create another type of ingress, if you need host based you will create another type of ingress. The one time thing of devops engineer is to choose which ingress controller they want to use - which load balancer they want to user- it can be nginx it can be f5. After the decision they can go to their respective github page and install the their controller and after that in their cluster they can create their desired service. 1 service, 2 service, 10 service they will only write the ingress resource.
Ingress is not 1 to 1 mapping, you can create one ingress and route 100s of services. 
So ingress is solving 2 problems:
    a. Enterprise level LB capabilities
    b. Cloud providers were charging for each and every IP addresses

Ingress controller has to be installed before ingress resource.

45. Activity

Using the previous setup we will do 'Host based' Load Balancing

    a. vi ingress.yml

        apiVersion: networking.k8s.io/v1
        kind: Ingress
        metadata:
        name: ingress-example
        spec:
        rules:
        - host: "foo.bar.com"
            http:
            paths:
            - pathType: Prefix
                path: "/bar"
                backend:
                service:
                    name: python-django-sample-app
                    port:
                    number: 80
        
        Now, simply apply:
        kubectl apply -f ingress.yml
        ---------

        Check:
        kubectl get ingress
        - lists all ingres

        Now although we have made this ingress, nothing will happen - what do I mean by that? What it means is that since I have created the ingress if I were to curl the domain 'foo.bar.com/bar' like this:
        curl -L http://foo.bar.com/bar -v
        Nothing will happen here..
        Why?
        Our desired output was that ingress should take me to the service- 'python-django-sample-app' as mentioned in the ingress that we have defined above. It is not happening because there is no ingress 'controller'.
        So, we will install ingress controller
        Search for it on Google for minikube:

        minikube addons enable ingress
        - installs nginx controller

        Let's check if it has installed. To check we will check pods because ingress controllers are also a pod
        kubectl get pods -A | grep nginx
        - lists pods in all namespace '-A' means all namespace

        Now let us find the logs and find out if it has identified our ingress resource that we have created.
        kubectl logs ingress-nginx-controller-cc8496... -n ingress-nginx
        - this command checks the logs of the mentioned pod in the specified namespace (nginx ingress created its own logs)

        You will find that it has successfully synced as well.

        If you enter the command 
        kubectl get ingress
        you will find here that now the Under the address there is an IP address earlier it was not there.

        In your minikube, you also have to edit the 'host'
        sudo vi /etc/hosts
        and add this :
        192.168.64.11 foo.bar.com
        Now if you try to ping it will be resolved.
                     




after completing this video watch this video positively for more details on Ingress controllers: https://www.youtube.com/watch?v=3YTU4EPjEh4


ConfigMaps & Secrets

ConfigMaps are used to store information. Suppose your app needs some information like DB port, DB username, connection type, etc., we know these information is retrieved used environment variables these details shouldn't be hardcoded because if details get changed user get null or no info at all
So we try to save this as an ENV variable
As a DevOps eng you can create a configmap inside a k8s cluster and put the information like DB port and any kind of information inside the configmap and you can mount the configmap or you can use the details of the configmap inside your k8s pod. 
So you can use this data of the configmap as ENV variables inside your k8s pod. You can use them by different ways like you can use them as ENV variables or you can use them as Volume Mounts. 
So, Configmap is solving the problem of stroring the information that can be used by your application at later point of time. 
Stored data can be used by your Pod, Deployment or your application.

Secrets - Secrets in k8s solves the same problem but it is used for sesitive data. Like parameters like DB password, DB Username.
Why we don't save this information in ConfigMap because whenever we save save anything in k8s this information is saved in etcd, and in etcd data is stored as objects and any hacker who tries to access the etcd they can get access to your infomation like DBpassword DB Username.  
So we should save all the non-sensitive data in ConfigMaps and Sensitive data in Secrets.
In Secrets k8s encrypts the data at rest. By default k8s uses the basic encryption but it also allows you to use your own encryption machanism - custom encryption.
K8s also says that although we're using an encryption for secrets you should do your part of using a strong RBAC. "Least Privilegde"
With secrets you can use a strong RBAC.

ConfigMaps
Activity:

    a. vi cm.yml

    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: test-cm
    data:
        db-port: "3306"
    
    kubectl apply -f cm.yml

    kubectl get cm

    kubectl describe cm test-cm

    Let's use this fields an environmental variable in a pod
    But for that we need to have a k8s pod. So, let's create a pod:

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
    
        kubectl apply -f deployement.yml

        kubectl get pods -w
        - watches the pods

        let's check the env variable of a pod of this deployement:

        kubectl exec -it sample-python-app-78578 -- /bin/bash
        - enters into the pod

        If you check here you will not find any environmental variable:
        env | grep db
        - checks for env variable in pod

        So to do this we have to add env in our deployment:
        vo deployment.yml

        Under image:
        env:
          - name: DB-PORT
            valueFrom:
              configMapKeyRef:
                name: test-cm
                key: db-port
        
        kubectl apply -f deployement.yml

        kubectl get pods -w
        - watches the pods in realtime.
        - you will see here that the previous pods are getting terminated and new ones are getting created.

        kubectl get pods
        - displays all the pods

        kubectl exec -it sample-python-app-78676 --bin/bash
        - enters into the pod
        - We have picked randomly any container for exec

        env | grep DB
        - searchs for env DB

        You will get the DB port as 3306
        So the developer can go to the python app and say
        os.env("DB-PORT") 
        and he will get the value for his database connection

        But now there is a problem
        What if we were to change the port from "3306" to "3307"
        and we make this change in our configmap.yml
        How will the pods come to know about this change?
        If you exec here to the pod we will find that the port is still the same 
        kubectl exec -it sample-python-app-78676 --bin/bash
        env | grep DB
        You will get the output DB-PORT=3306
        So the port has not changes despite changing it in our configmap.yml
        To solve this problem k8s says instead of using this approach, go with the approach of VolumeMounts
        VolumeMounts
        In VolumeMounts, instead of using them as environmental variables you can use them as files. So you configmap information can be saved inside a file and developers can read the files instead of env variables.
        let's see how to do it

        vi deployment.yml
        Delete the environment (entire content of env) and replace it with:
        Keep in mind that to mount any volume you need to create a volume first
        At the level of the 'containers', write 'volumes' like:
        volumes:
          - name: db-connection
            configMap:
              name: test-cm

        - In the above volume, we have kept 'configmap' to take out the info
        
        And under 'image':
        volumeMounts:
          - name: db-connection
            mountPath: /opt
        
        - In the above volumeMount, we have kept the mountPath as '/opt' but it can be anything.

        Let's apply:
        kubectl apply -f deployment.yml

        kubectl get pods -w
        - watches the pods realtime

        Get the pod name to exec
        kubectl get pods

        kubectl exec -it sample-python-app-7878687 --/bin/bash
        let's see if the volume is mouted on the '/opt' directory
        ls /opt
        Output: db-port
        - this means it is mounted
        Let's find the value of the db-port
        cat /opt/db-port | more
        Output: 3306
        So, it got mounted appropriately in the file-system 
        
        Now, let's change the port in the configmap and see if it gets updated. for this, let's edit the configmap:
        vi configmap.yml
        change the port from '3306' to '3307'
        and apply the changes:
        kubectl apply -f cm.yml
        
        Now, what will happen is the kubernetes pod, without getting restarted will know that the value of the configmap has changed. Let's find out:
        Fisrt let's make sure that the port you have applied has actually changed:

        kubectl describe cm test-cm
        - decribes the referred configmap(cm)- here 'test-cm'
        You will find here that the value has changed

        Let's also ensure that the pods have not restarted:
        kubectl get pods
        - lists the pods
        - check the AGE to find whether they have restarted or not
        Now let's exec and see:
        kubectl exec -it sample-python-app-675675776 -- /bin/bash
        cat /opt/dp-port
        it got changed!

        Change it again in the cm
        vi cm.yml
        Under 'data:' in db-port: "3307"
        change the port number to "3309"
        kubectl apply -f cm.yml

        You don't have to exec everytime we can modify the command to display the info like:
        kubectl exec -it sample-python-app-7686 --cat/opt/db-port

        You will find it got changed after some time

Secrets
Activity

        a. kubectl create secret generic test-secret --from -literal=db-port="3306"
            - creates secret from the cli itself
            - there are several kinds of secrets like 'tls secret', 'generic secret'. 'tls secrets' are basically used to store the certificates.

        kubectl describe secret test-secret
        - Describes the secret
        - Let's find if this is encrypted or not (since it is a secret)

        kubectl edit secret test-secret
        - opens the referred secret yml
        - you will find here that the db-port is encrypted in base64. This is not the best encryption though.
        - Kubernetes allows you to encrypt secret. You can encrypt it using HashiCorp vault, Seal secret, etc.,
        - if you want to find out what is the encrypted by coming out of yml and type the following command:
        echo MzMwNg== | base64 --decode
        - Output: 3306
        - decrypts the port number
        - encryption is done on etcd (we will discuss later)

RBAC - Role Based Access Control

45. Can be broadly devided into 2 parts:
    a. Users
    b. Service Accounts

    a. Users 



    





              








