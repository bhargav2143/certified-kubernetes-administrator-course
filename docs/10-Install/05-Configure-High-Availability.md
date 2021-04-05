# Choosing a HA

  Take me to [Lecture](https://kodekloud.com/courses/539883/lectures/9808328)
  
what happens when you lose the master node in your cluster?

As long as the workers are up and containers are alive, your applications are still running.
Users can access the application until things start to fail.
For example a container or POD on the worker node crashes. Now, if that pod was part of a replicaset then the replication controller on the master needs to instruct
the worker to load a new pod. But the Master is not available and so are the controllers and schedulers on the master.
There is no one to recreate the pod and no one to schedule it on nodes.

Similarly, since the kube-api server is not available you cannot access the cluster externally through
kubectl tool or through API for management purposes. 
Which is why you must consider multiple master nodes in in a High availability configuration in your production environment. 
A high availability configuration is where you have redundancy across every component in the cluster so as to avoid a single point of failure.

HA - Api server
We know that the API server is responsible for receiving requests and processing them or providing information about the cluster. They work on one request at a time.
So the API servers on all cluster nodes can be alive and running at the same time in an active active mode
we know that the kubectl utility talks to the API server to get things done and we point the kubectl utility to reach the master node at port 6443.
That’s where the API server listens and this is configured in the kube-config file.

Well now with 2 masters, where do we point the kubectl to?
We can send a request to either one of them but we shouldn't be sending the same request to both of them.
So it is better to have a load balancer of some kind configured in the front of the master nodes that split traffic between the API servers.
So We then point the kubectl utility to that load balancer. You may use, NGINX or HA proxy or any other load balancer for this purpose.

What about the scheduler and the controller manager.?

These are controllers that watch the State of the cluster and take actions. For example the controller
manager consists of controllers like the replication controller that is constantly watching the state
of PODs and taking necessary actions, like creating a new POD when one fails. If multiple instances of those run in parallel, then they might duplicate actions
resulting in more parts than actually needed. The same is true with scheduler. As such they must not run in parallel.
They run in an active standby mode.

So then who decides which among the two is active and which is passive.
This is achieved through a leader election process.
So how does that work.

Let's look at controller manager for instance. When a controller manager process is configured.
You may specify the leader elect option which is by default set to true.
With this option when the controller manager process starts it tries to gain a lease or a lock on an
endpoint object in kubernetes named as kube-controller-manager endpoint. Which ever process first
updates the endpoint. With this information gains the lease and becomes the active of the two.
The other becomes passive it holds the lock for the lease duration specified using the leader-elect-lease-duration option which is by default set to 15 seconds.
The active process then renews the lease every 10 seconds which is the default value for the option leader-elect-renew-deadline.
Both the processes tries to become the leader every two seconds, set by the leader-elect-retry-period option.

That way if one process fails maybe because the first must of crashes then the second process can acquire
the lock and become the leader. The scheduler follows a similar approach and has the same command line

ETCD.
there are two topologies that you can configure in kubernetes.
One is as it looks here and the same architecture we have been following through out this course, where
ETCD is part of the kubernetes master nodes. It’s called as a stacked control plan nodes topology.
This is easier to setup and easier to manage. And requires fewer nodes. But if one node goes down
both an ETCD member and control plane instance is lost and redundancy is compromised. 
The other is where ETCD is separated from the control plane nodes and run on its own set of servers. This is a topology
with external ETCD servers. Compared to the previous topology this is less risky as a failed control plane node does not impact the ETCD cluster and the data it stores.
However it is harder to setup and requires twice the number of servers for the external etcd nodes. So
remember, the API server is the only component that talks to the ETCD server and if you look into the
API service configuration options, we have a set of options specifying where the ETCD server is.
So regardless of the topology we use and wherever we configure ETCD servers, weather on the same server
or on a separate server
ultimately we need to make sure that the API server is pointing to the right address of the ETCD servers.
Now remember ETCD is a distributed system, so the API server or any other component that wishes to talk
to it, can reach the ETCD server at any of its instances.

You can read and write data through any of the available ETCD server instances.
This is why we specify a list of etcd-servers in the kube-apiserver configuration.



![image](https://user-images.githubusercontent.com/40159115/113578991-4b948f80-9641-11eb-8267-24d967a23aa3.png)
