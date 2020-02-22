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


# NETRWORKING IN KUBERNETES


# KUBERNETES ARCHITECTURE AND COMPONENTS