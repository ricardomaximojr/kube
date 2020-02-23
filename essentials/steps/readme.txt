# CONTAINERS AND PODS

In order to run and manage containers with Kubernetes, you will need to use pods. In this lesson, we discuss the basics of what pods are and how they are related to containers within the world of Kubernetes. We will create a simple pod and then we will look at some ways to explore and interact with pods in your Kubernetes cluster.

Here are the commands used in this lesson:

Create a simple pod running an nginx container:
cat << EOF | kubectl create -f -
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
EOF

Get a list of pods and verify that your new nginx pod is in the Running state:
kubectl get pods

Get more information about your nginx pod:
kubectl describe pod nginx

Delete the pod:
kubectl delete pod nginx

# CLUSTERING AND NODES
Nodes are an essential part of the Kubernetes cluster. They are the machines where your cluster's container workloads are executed. In this lesson, we will discuss what nodes are in Kubernetes, and we will explore some ways in which you can find information about nodes in your cluster.

Here are the commands used in this lesson:

Get a list of nodes:
kubectl get nodes

Get more information about a specific node:
kubectl describe node $node_name

# NETRWORKING IN KUBERNETES

Networking is an important part of understanding the basics of Kubernetes. This lesson provides a high-level overview of what a Kubernetes virtual cluster network looks like. We will also demonstrate how the network functions by contacting one pod from another pod over the virtual network.

Create a deployment with two nginx pods:
    cat << EOF | kubectl create -f -
    apiVersion: apps/v1
    kind: Deployment
    metadata:
        name: nginx
        labels:
            app: nginx
    spec:
        replicas: 2
        selector:
            matchLabels:
            app: nginx
        template:
            metadata:
            labels:
                app: nginx
            spec:
            containers:
            - name: nginx
                image: nginx:1.15.4
                ports:
                - containerPort: 80
    EOF
Create a busybox pod to use for testing:
    cat << EOF | kubectl create -f -
    apiVersion: v1
    kind: Pod
    metadata:
    name: busybox
    spec:
    containers:
    - name: busybox
        image: radial/busyboxplus:curl
        args:
        - sleep
        - "1000"
    EOF
Get the IP addresses of your pods:
    kubectl get pods -o wide
Get the IP address of one of the nginx pods, then contact that nginx pod from the busybox pod using the nginx pod's IP address:
    kubectl exec busybox -- curl $nginx_pod_ip


# KUBERNETES ARCHITECTURE AND COMPONENTS

A Kubernetes cluster is made up of multiple individual components running on the various machines that are part of the cluster. In this lesson, we will briefly discuss the major Kubernetes software components and what each of them do. We will also look into how these components are actually running in our cluster currently.

Here are the commands used in this lesson:

Get a list of system pods running in the cluster:
    kubectl get pods -n kube-system

Check the status of the kubelet service:
    sudo systemctl status kubelet


# KUBERNETES DEPLOYMENTS

Pods are a great way to organize and manage containers, but what if I want to spin up and automate multiple pods?

'Deployments' are a great way to automate the management of your pods. A deployment allows you to specify a 'desired state' for a ste of pods. The cluster will then constantly work to maintain that desired state.

For example:
- 'Scaling' - with a deployment, you can specify the number of replicas you want, and the deployment will create(or remove) pods to meet the number of repolicas
- 'Rolling Updates' - with a deployment, you can change the deployment container image to a new version of the image. The deployment will gradually replace existing containers with the new version
- 'Self-Healing' - If one of the pods in the deployment is accidentally destroyed, the deployment will immediately spin up a new one to replace it.

Deployments are an important tool if you want to take full advantage of the automation capabilities provided by Kubernetes. In this lesson, we will discuss what deployments are and briefly mention some common use cases for Kubernetes deployments. We will also create a simple deployment in our cluster and explore how we can interact with it.

Here are the commands used in this lesson:

Create a deployment:
    cat <<EOF | kubectl create -f -
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    name: nginx-deployment
    labels:
        app: nginx
    spec:
    replicas: 2
    selector:
        matchLabels:
        app: nginx
    template:
        metadata:
        labels:
            app: nginx
        spec:
        containers:
        - name: nginx
            image: nginx:1.15.4
            ports:
            - containerPort: 80
    EOF

Get a list of deployments:
    kubectl get deployments

Get more information about a deployment:
    kubectl describe deployment nginx-deployment

Get a list of pods:
    kubectl get pods
You should see two pods created by the deployment.

# KUBERNETES SERVICES

'Services' are another important component of deploying apps in K8s.

Services allow you to dynamically access a group of replica pods. Replica pods are often being created and destroyed, so what happens to others pods or external entities which need to access those pods.

A Service creates an 'abstraction layer' on top of a set of replica pods. You can access the service rather than accessing the pods directly, so as pods come and go, you get uninterrupted, dynamic access to whatever replicas are up at the time.

While deployments provide a great way to automate the management of your pods, you need a way to easily communicate with the dynamic set of replicas managed by a deployment. This is where services come in. In this lesson, we will discuss what services are in Kubernetes, demonstrate how to create a simple service, and explore that service in our own cluster.

Here are the commands used in the demonstration:

Create a NodePort service on top of your nginx pods:
    cat << EOF | kubectl create -f -
    kind: Service
    apiVersion: v1
    metadata:
    name: nginx-service
    spec:
    selector:
        app: nginx
    ports:
    - protocol: TCP
        port: 80
        targetPort: 80
        nodePort: 30080
    type: NodePort
    EOF

Get a list of services in the cluster.
    kubectl get svc
You should see your service called nginx-service.

Since this is a NodePort service, you should be able to access it using port 30080 on any of your cluster's servers. You can test this with the command:
    curl localhost:30080
You should get an HTML response from nginx
