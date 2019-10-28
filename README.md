# Exposing an External IP Address to Access an Application in a Cluster in Minikube

Minikube (kubernetes deployment locally on linux machine)
---------------------------------------------------------
Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a VM


Install kubectl
(Installed from here locally:)

https://kubernetes.io/docs/tasks/tools/install-kubectl/

Install Minikube
(Installed from here locally:)

https://github.com/kubernetes/minikube/releases


```
$ minikube start --vm-driver=hyperkit   (Run kubernetes components locally if you are using mac)

Get the IP of the local kubernetes cluster: $minikube ip
SSH into the minikube VM: $minikube ssh
$minikube dashboard
$minikube service list
```

Execute locally:
-----------------
```

$ minikube start (default driver is vbox)
(spin up minikub VM in vobx, kubernetes server/master start)

$ minikube status (show status like ip)
$ minikube dashboard (Open dashboard in browser)
$ minikube stop (stop kubernetes server/master)


$ kubectl version
$ kubectl config current-context
$ kubectl cluster-info
$ kubectl cluster-info dump
$ kubectl get nodes
$ kubectl get services
$ kubectl get deployment
$ kubectl get pods
$ kubectl get pods --all-namespaces
$ kubectl get events

```

Run five instances of a Hello World application.
Create a Service object that exposes an external IP address.
Use the Service object to access the running application.

Run a Hello World application in your cluster:

`
kubectl apply -f https://k8s.io/examples/service/load-balancer-example.yaml
`

The preceding command creates a Deployment object and an associated ReplicaSet object. The ReplicaSet has five Pods, each of which runs the Hello World application.

Display information about the Deployment:

```
kubectl get deployments hello-world
kubectl describe deployments hello-world
```

Display information about your ReplicaSet objects:

```
kubectl get replicasets
kubectl describe replicasets
```

Create a Service object that exposes the deployment:

```
kubectl expose deployment hello-world --type=LoadBalancer --name=my-service
```
Display information about the Service:

`
kubectl get services my-service
`
The output is similar to this:

```
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
my-service   LoadBalancer   10.96.109.232   <pending>     8080:31518/TCP   4m5s
```

Display detailed information about the Service:

`
kubectl describe services my-service
`
The output is similar to this:

```
Name:                     my-service
Namespace:                default
Labels:                   app.kubernetes.io/name=load-balancer-example
Annotations:              <none>
Selector:                 app.kubernetes.io/name=load-balancer-example
Type:                     LoadBalancer
IP:                       10.96.109.232
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31518/TCP
Endpoints:                172.17.0.10:8080,172.17.0.13:8080,172.17.0.14:8080 + 2 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
Ayeshas-MacBook-Pro:simple-hello-world-app-deployment ayeshakaleem$ kubectl describe services my-service
Name:                     my-service
Namespace:                default
Labels:                   app.kubernetes.io/name=load-balancer-example
Annotations:              <none>
Selector:                 app.kubernetes.io/name=load-balancer-example
Type:                     LoadBalancer
IP:                       10.96.109.232
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31518/TCP
Endpoints:                172.17.0.10:8080,172.17.0.13:8080,172.17.0.14:8080 + 2 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```
 
check minikube ip by using following command.

`
minikube ip
`
you will get the ip then curl in your browser

```
curl http://<minikube-ip>:<port>
for example:
http://192.168.64.3:31518/

```

In the preceding output, you can see that the service has several endpoints: 10.0.0.6:8080,10.0.1.6:8080,10.0.1.7:8080 + 2 more. These are internal addresses of the pods that are running the Hello World application. To verify these are pod addresses, enter this command:

`
kubectl get pods --output=wide
`
The output is similar to this:

```
NAME                          READY   STATUS              RESTARTS   AGE     IP            NODE       NOMINATED NODE   READINESS GATES
hello-world-f9b447754-7ccjm   1/1     Running             0          3h35m   172.17.0.16   minikube   <none>           <none>
hello-world-f9b447754-89hfv   1/1     Running             0          3h35m   172.17.0.10   minikube   <none>           <none>
hello-world-f9b447754-gc9g5   1/1     Running             0          3h35m   172.17.0.14   minikube   <none>           <none>
hello-world-f9b447754-pwwmt   1/1     Running             0          3h35m   172.17.0.13   minikube   <none>           <none>
hello-world-f9b447754-z9hld   1/1     Running             0          3h35m   172.17.0.15   minikube   <none>           <none>
```
Use the minikube IP address to access the Hello World application:

Typing minikube service my-service will automatically open the Hello World application in a browser.

The response to a successful request is a hello message:

`
Hello Kubernetes!
`

**Cleaning up**
To delete the Service, enter this command:

`
kubectl delete services my-service
`
To delete the Deployment, the ReplicaSet, and the Pods that are running the Hello World application, enter this command:

`
kubectl delete deployment hello-world
`
