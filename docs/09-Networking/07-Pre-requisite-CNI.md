# Pre-requisite CNI

  - Take me to [Lecture](https://kodekloud.com/courses/539883/lectures/9808284)

In this section, we will take a look at **Pre-requisite Container Network Interface(CNI)**
The CNI is a set of standards that define how programs
should be developed to solve networking challenges in a container runtime environment.
Plugin - simply call iy bridge -The program or a script that performs all the required tasks to get the container attached 
to a bridge network.
Docker does not implement CNI. Docker has its own set of standards known as CNM which
stands for Container Network Model which is another standard that aims at solving container networking.
When kubernetes creates docker containers it creates them on the none network.
It then invokes the configured CNI plugins who takes care of the rest of the configuration.
![net-7](../../images/net7.PNG)



## Third Party Network Plugin Providers

- [Weave](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/#-installation)
- [Calico](https://docs.projectcalico.org/getting-started/kubernetes/quickstart)
- [Flannel](https://github.com/coreos/flannel/blob/master/Documentation/kubernetes.md)
- [Cilium](https://github.com/cilium/cilium)


## To view the CNI Network Plugins

- CNI comes with the set of supported network plugins. 

```
$ ls /opt/cni/bin/
bridge  dhcp  flannel  host-device  host-local  ipvlan  loopback  macvlan  portmap  ptp  sample  tuning  vlan
```




#### References Docs

- https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/


