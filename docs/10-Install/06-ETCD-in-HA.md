# ETCD in HA

  Take me to [Lecture](https://kodekloud.com/courses/539883/lectures/9808331)
  
  
ETCD ensures that the same consistent copy of the data is available on all instances at the same time.

With reads, Since the same data is available across all nodes,you can easily read it from any nodes. 

But that is not the case with writes.
What if two write requests come in on two different instances? Which one goes through?
ETCD does not process the writes on each node.
Instead, only one of the instances is responsible for processing the writes. Internally
the two nodes elect a leader among them. Of the total instances one node becomes the leader and the other
node becomes the followers.
If the writes came in through the leader node, then the leader processes the write. The leader makes sure that the other nodes are sent a copy of the data.
If the writes come in through any of the other follower nodes, then they forward the writes to the leader internally and then the leader processes the writes. 
Again when the writes are processed, the leader ensures that copies of the write and distributed to other instances in the cluster.
Thus a write is only considered complete, if the leader gets consent from other members in the cluster.

So how do they elect the leader among themselves? And how do they ensure a write is propogated across all instances? 
ETCD implements distributed consensus using RAFT protocol.

Let's see how that works in a three node cluster.
When the cluster is setup we have 3 nodes that do not have a leader elected. RAFT algorithm uses
random timers for initiating requests.
For example a random timer is kicked off on the three managers the first one to finish the timer sends
out a request to the other node requesting permission to be the leader. The other managers on receiving
the request responds with their vote and the node assumes the Leader role. Now that it is elected the
leader it sends out notification at regular intervals to other masters informing them that it is continuing
to assume the role of the leader. In case the other nodes do not receive a notification from the leader
at some point in time which could either be due to the leader going down or losing network connectivity
the nodes initiate a re-election process among themselves and a new leader is identified going back
to our previous example where a right come seen it is processed by the leader and is replicated to other
nodes in the cluster the right is considered to be complete only once it is replicated to the other
instances in the cluster.

A write is considered to be complete, if it can be written on the majority of the nodes in the cluster. For example, in this case of the 3 nodes, the majority is 2.
 Quorum is the minimum number of nodes that must be available for the cluster to function properly
 When deciding on the number of master nodes,it is recommended to select an odd number as 3 or 5 or 7
 No matter how the network segments there are better chances for your cluster to stay alive in case of network segmentation with odd number of nodes.
So an odd number of nodes is preferred over even number having 5 is preferred over 6 and having
more than 5 nodes is really not necessary as 5 gives you enough for tolerance.
