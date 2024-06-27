# Kubernetes learning note

## Onwards to Kubernetes
---

- <img
  src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/k8s1.png">
- What is Kubernetes? - A system for running many different containers over
  multiple different machines.
- Why use k8s? - When you need to run many different containers with different
  images.
- Working with k8s:
  - For dev purpose, usually use minikube
  - For prod purpose, options are Azure, AWS, GCP, and self deploy.
- <img
  src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/k8s2.png">
- <img
  src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/k8s3.png">
- <img
  src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/k8s4.png">
- "Object" is created by yaml file, example object types are:
  - StatefulSet
  - ReplicaController
  - pod
  - service
- Objects serve different purposes, like running a container, monitoring a
  container, setting up networking, etc.
- The apiVersion (e.g. v1, or apps/v1) defines a different set of object we can use.
- k8s can not deploy container, instead, deploy pod. In one pod, containers are
  all in similiar functional feature, and in related relationship.
- One of the object "NodePort" (only good for dev purpose) can use selector in
  yaml file to find every other object with the same label and expose its port
  xxxx to the outside of the world.
- ```kubectl apply -f <filename>``` -> this is the cli we use to change our k8s
  cluster.
- ```kubectl get pods```
- ```kubectl get services```
- ```minikube ip``` -> return vm ip
- <img
  src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/k8s5.png">
- Based on the graph above, we know that if one container inside pod get stopped
  / killed / crashed, k8s will help to automatically restart a new one. This is
  thanks to master who keep observing the required number and current number of
  container.
- Master is machine or vm with a set of programs to manage nodes.
- k8s determine where to run each container, which means each node can run a
  dissimiliar set of containers.
- To deploy something, we update the desired state of the master with a config
  file.
- Deployment of master:
  - Imperative deployment: tell master what to do and how to do in detailed.
  - Declarative deployment: give requirement in config file, then let master
    decide how to achieve that requirement.

## Maintaining sets of containers with deployments
---

- ```minikube start --driver=hyperkit```
- ```kubectl apply -f <filename>```
- ```kubectl describe <object type><object name>```
- <img
  src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/k8s6.png">
- In real world, if in yaml file, the object type we use is pod kind, then many
  attributes are not able to modify afterwards, e.g. port we can not modify it
  afterwards. In this case, deployment kind is better. Sometimes, we use
  deployment kind to replace pod kind.
- cli for removing an object:
  - ```kubectl delete -f <config file>```
- Let's think about why service object is necessary. Actually, the container
  owns its internal ip, by running ```kubectl get pods -o wide``` we can get the
  ip of the pod. However, whenever this pod is replaced or crashed, the internal
  ip can change, so why not letting service to hold a constant ip? Then the
  service object select all port 9999 requests to pods which with the 'label'
  through selector.
- After you update image, to do docker build image, then docker push to remote
  dockerhub.
- But, after the image get updated from remote, it is hard for local to find the
  update by kubectl apply file, which caused sometimes hard to update local.
- TODO: graph for solution

## Docker daemon
---
- minikube is a lightweight k8s implementation that creates a vm on your local
  machine and deploys a simple cluster containing only one node.
- Docker desktop runs a k8s single node cluster just like monikube, but it does
  not actually run monikube.
- Some time we would want to get over master to directly enter the vm, by using
  ```eval $(minikube -p minikube docker-env)```. The reason why we want to do
  that is 1) now docker clis are all available to be used. 2) kill containers to
  test k8s self-heal 3) delete cache image in the node.
  
## Some thoughts on prod level:
---

- Concept for kube:
  - kubectl: the command to talk to the cluster.
  - kubelet: the component that runs on all of the machines in your cluster and
    soes things like starting pods and containers.
  - kubeadm: the command to bootstrap the cluster.
- As we previous discussed about the volume, remember that some container's
  replicas is 2+, which can not use same volume, which will cause collapse.
- In k8s, we use persistent volume claim or persistent volume.
- <img
  src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/k8s7.png">
- Object secrets in k8s: securely stores a piece of info in the cluster, such as
  a database passwork. Secret does not need config, by cli can be done.
  - ```kubectl create secret generic <secret_name> -- from-literal .
    key-value```


## Handling traffic with ingress controllers
---

- <img
  src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/k8s7.png">
- In k8s, object is deployed by controller through writing config file.


Reference:
https://www.udemy.com/certificate/UC-730cb976-ae30-464c-9275-d9d8bf16a47d/
