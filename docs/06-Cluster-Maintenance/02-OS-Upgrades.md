# OS Upgrades
  - Take me to [Video Tutorial](https://kodekloud.com/courses/539883/lectures/9808229)
  
In this section, we will take a look at os upgrades.

#### If the node was down for more than 5 minutes, then the pods are terminated from that node.
--pod-eviction-timeout duration     Default: 5m0s
The grace period for deleting pods on failed nodes.

  ![os](../../images/os.PNG)
  
- You can purposefully **`drain`** the node of all the workloads so that the workloads are moved to other nodes.
  ```
  $ kubectl drain node-1
  ```
- The node is also cordoned or marked as unschedulable.
- When the node is back online after a maintenance, it is still unschedulable. You then need to uncordon it.
  ```
  $ kubectl uncordon node-1
  ```
- There is also another command called cordon. Cordon simply marks a node unschedulable. Unlike drain it does not terminate or move the pods on an existing node.

  ![drain](../../images/drain.PNG)
  
  
#### K8s Reference Docs
- https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
