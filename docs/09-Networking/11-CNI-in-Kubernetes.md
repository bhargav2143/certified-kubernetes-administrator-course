# CNI in Kubernetes

  - Take me to [Lecture](https://kodekloud.com/courses/certified-kubernetes-administrator-with-practice-tests/lectures/9808289)

In this section, we will take a look at **Container Networking Interface (CNI) in Kubernetes**
In this lecture we will see how kubernetes is configured to use these network plugins. As we discussed in the
pre-requisite lecture CNI defines the responsibilities of container runtime. As per CNI, container runtimes,
in our case Kubernetes, is responsible for creating container network namespaces, identifying and
attaching those namespaces to the right network by calling the right network plugin.
So where do we specify the CNI plugins for Kubernetes to use? The CNI plugin must be invoked by the
component within Kubernetes that is responsible for creating containers. Because that component
must then invoke the appropriate network plugin after the container is created.
The CNI plugin is configured in the kubelet service on each node in the cluster.
If you look at the kubelet service file, you will see an option called network-plugin set to CNI.
## Configuring CNI

![net-1](../../images/net1.PNG)


- Check the status of the Kubelet Service

```
$ systemctl status kubelet.service
```

## View Kubelet Options

```
$ ps -aux | grep kubelet
```

## Check the Supportable Plugins 

- To check the all supportable plugins available in the `/opt/cni/bin` directory.

```
$ ls /opt/cni/bin

```

## Check the CNI Plugins

- To check the cni plugins which kubelet needs to be used.

```
ls /etc/cni/net.d

```
If there are multiple files here,It will choose the one in alphabetical order.

This is a format defined by the CNI standard for a plugin configuration file.

. It’s name is mynet, type is bridge.

 The isGateway defines whether the bridge network interface should get an IP address assigned so it can act as a gateway.
 The ipMasquerade defines if a NAT rule should be added for IP masquerading. 
 The IPAM section defines IPAM configuration.
This is where you specify the subnet or the range of IP addresses that will be assigned to pods and any necessary routes. 
The type host-local indicates that the IP addresses are managed locally on this host.
Unlike a DHCP server maintaining it remotely. The type can also be set to DHCP to configure an external DHCP server.
## Format of Configuration File  

![net-2](../../images/net2.PNG)


#### References Docs

- https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/
- https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/
