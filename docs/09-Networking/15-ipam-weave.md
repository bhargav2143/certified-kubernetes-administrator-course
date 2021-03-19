# IPAM weave

  - Take me to [Lecture](https://kodekloud.com/courses/certified-kubernetes-administrator-with-practice-tests/lectures/9808295)

- IP Address Management in the Kubernetes Cluster

![net-3](../../images/net3.PNG)


- How weaveworks Manages IP addresses in the Kubernetes Cluster 
Weave by default allocates the ip address range 10.32.0.0/12 for the entire network.
The peers decide to split the IP addresses equally between them and assigns one portion to each node
pods created On this note we'll have is in this range.
Of course these ranges are configurable with additional options past while deploying the weave plugin
![net-4](../../images/net4.PNG)


## References Docs

- https://www.weave.works/docs/net/latest/kubernetes/kube-addon/
- https://kubernetes.io/docs/concepts/cluster-administration/networking/ 
