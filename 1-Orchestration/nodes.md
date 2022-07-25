# Nodes

You can run `docker node inspect self` to view details on the current node.

To exclude a manager node from running the tasks of a service: `docker node update --availability drain manager`.

Setting a node to DRAIN does not remove standalone containers from that node, such as those created with docker run, docker-compose up, or the Docker Engine API.

## Achieving Quorum

In a swarm of N managers, a quorum (a majority) of manager nodes must always be available. For example, in a swarm with five managers, a minimum of three must be operational and in communication with each other. 

The swarm can tolerate up to (N-1)/2 permanent failures, beyond which requests involving swarm management cannot be processed. An odd number of managers is recommended because the next even number does not make the quorum easier to keep. For instance, whether you have 3 or 4 managers, you can still only lose 1 manager and maintain the quorum. If you have 5 or 6 managers, you can still only lose two.

Even if a swarm loses the quorum of managers, swarm tasks on existing worker nodes continue to run. However, swarm nodes cannot be added, updated, or removed, and new or existing tasks cannot be started, stopped, moved, or updated.
