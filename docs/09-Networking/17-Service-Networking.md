# Service Networking

  - Take me to [Lecture](https://kodekloud.com/courses/certified-kubernetes-administrator-with-practice-tests/lectures/9808291)

In this section, we will take a look at **Service Networking**


How are the services getting these IP addresses and how are they made available across all the nodes?

 Kube proxy watches the changes in the cluster through kube-api server, and every time a new service is to be created, kube-proxy gets into action. Services are a cluster wide concept. They exist across all the nodes in the cluster
It's just a virtual object.
Then how do they get an IP address and how were we able to access the application on the pod through service?
When we create a service object in kubernetes, it is assigned an IP address from a pre-defined range.
The kube-proxy components running on each node, get’s that IP address and creates forwarding rules on
each node in the cluster, saying any traffic coming to this IP, the IP of the service, should go to the IP of the POD.
Now remember it's not just the IP it's an IP and port combination.

So how are these rules created? 
kube-proxy supports different ways, such as userspace where kube-proxy
listens on a port for each service and proxies connections to the pods. 
If this is not set, it defaults to iptables.
## Service Types


- ClusterIP 


```
clusterIP.yaml

apiVersion: v1
kind: Service
metadata:
  name: local-cluster
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: nginx
```

- NodePort

```
nodeportIP.yaml

apiVersion: v1
kind: Service
metadata:
  name: nodeport-wide
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: nginx
```

## To create the service 

```
$ kubectl create -f clusterIP.yaml
service/local-cluster created

$ kubectl create -f nodeportIP.yaml
service/nodeport-wide created
```

## To get the Additional Information

```
$ kubectl get pods -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP           NODE     NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          1m   10.244.1.3   node01   <none>           <no
```

## To get the Service

```
$ kubectl get service
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP        5m22s
local-cluster   ClusterIP   10.101.67.139   <none>        80/TCP         3m
nodeport-wide   NodePort    10.102.29.204   <none>        80:30016/TCP   2m
```

## To check the Service Cluster IP Range 

```
$ ps -aux | grep kube-apiserver
--secure-port=6443 --service-account-key-file=/etc/kubernetes/pki/sa.pub --
service-cluster-ip-range=10.96.0.0/12

```

## To check the rules created by kube-proxy in the iptables

```
$ iptables -L -t nat | grep local-cluster
KUBE-MARK-MASQ  all  --  10.244.1.3           anywhere             /* default/local-cluster: */
DNAT       tcp  --  anywhere             anywhere             /* default/local-cluster: */ tcp to:10.244.1.3:80
KUBE-MARK-MASQ  tcp  -- !10.244.0.0/16        10.101.67.139        /* default/local-cluster: cluster IP */ tcp dpt:http
KUBE-SVC-SDGXHD6P3SINP7QJ  tcp  --  anywhere             10.101.67.139        /* default/local-cluster: cluster IP */ tcp dpt:http
KUBE-SEP-GEKJR4UBUI5ONAYW  all  --  anywhere             anywhere             /* default/local-cluster: */
```

## To check the logs of kube-proxy

- May this file location is vary depends on your installation process.

```
$ cat /var/log/kube-proxy.log

```


#### References Docs

- https://kubernetes.io/docs/concepts/services-networking/service/
