# Overlay Networking

`docker network create -d overlay uber-net`

If you want to encrypt the data plan, you add the `-o encrypted` flag to the command.

Control plane traffic is cluster management traffic, whereas data plane traffic is application traffic.

By default, Docker overlay networks encrypt cluster management traffic, but not application traffic. You must explicitly enable encryption of application traffic.

You only see overlay networks on worker nodes when they are tasked with running a container on it. This reduces network gossip!

## Attaching a Service to an Overlay Network

`docker service create --name test --network uber-net --replicas 2 ubuntu sleep infinity`

Standalone containers that are not part of a swarm service cannot attach to overlay networks unless they have the `attachable` property:

`docker network create -d overlay --attachable uber-net`