# Networking

If you run `docker network ls`, the Docker engine creates three default networks for you:

- **none:** the loopback (lo) interface for local communication within a container.
- **host:** the container gets attached to the host network stack and shares the host's IP addresses and ports.
- **bridge**: the default network if the network isn't configured using the `--net` option of the `docker run` subcommand.

Docker networking is based on an open source pluggable architecture called the Container Network Model (CNM).

`libnetwork` is Docker's real-world implementation of the CNM and it provides all Docker's core networking capabilities. Drivers plug into `libnetwork` to provide specific network topologies.

`libnetwork` provides a native service discovery and basic container load balancing solution.

At the highest level, Docker networking comprises three major components:

- The Container Network Model (CNM)
- `libnetwork`
- Drivers (to extend the model)

## The Container Network Model (CNM)

The CNM defines three major building blocks:

- Sandboxes
- Endpoints
- Networks

A **sandbox** is an isolated network stack. It includes ethernet interfaces, ports, routing tables, and DNS config. Sandboxes are placed inside containers to provide network connectivity.

**Endpoints** are virtual network interfaces (e.g. `veth`). In the CNM, it's the job of the *endpoint* to connect a *sandbox* to a *network*. Endpoints can only connect to a single network. If a container needs to connect to multiple networks, it needs multiple endpoints.

**Networks** are a software implementation of a switch. They group together and isolate a collection of endpoints that need to communicate.

### Drivers

Drivers implement the data plane - connectivity, isolation, and network creation are all handled by drivers.

Docker ships with built-in drivers:

- bridge
- overlay - when you initialize a swarm, Docker creates an ingress network that uses the `overlay` driver by default.
- macvlan (Swarm)

Each driver is in charge of the creation and management of all resources on the networks for which it's responsible. 

`libnetwork` allows multiple network drivers to be active at the same time.

## Single-Host Bridge Networks

This is the simplest type.

It only exists on a single Docker host and can only connect containers on the same host.

Docker creates single-host bridge networks with the built-in `bridge` driver.

Every Docker host gets a default single-host bridge network. This is the network to which all new containers are connected, unless you override it on the command line with the `--network` flag.

To list networks:

`docker network ls`

To inspect a network:

`docker network inspect <network-name>`

To create a network:

`docker network create -d bridge <network-name>`

Attach a new container to a network:

`docker container run -d --name c1 --network localnet alpine sleep 1d`

You'll then see that container when you run the `docker network inspect` command.

If you add another new container to the same network, it should be able to ping the *c1* container by name. This is because all new containers are automatically registered with the embedded Docker DNS service, enabling them to resolve the names of all other containers on the same network.

*The default bridge network doesn't support name resolution, only user-defined bridge networks.*

Although containers on bridge networks can only communicate with other containers on the same network, you can get around this with *port mappings*.

Port mappings let you map a container to a port on the Docker host. Any traffic hitting the Docker host on the configured port is directed to the container.

You can find a container's port with:

`docker port web`

Only a single container can bind to any port on the host. This means no other containers on that host can bind to that specific port. This is one of the reasons why single-host bridge networks are only useful for local development and very small applications.

## Multi-Host Overlay Networks

Overlay networks allow a single network to span multiple hosts so containers on different hosts can communicate directly.

## Connecting to Existing Networks

The built-in `MACVLAN` driver makes containers first-class citizen on existing physical networks by giving each one its own MAC address and IP address.

Due to the security requirements, MACVLAN is great for corporate data center networks, but probably wouldn't work in the public cloud.

## Service Discovery

Service discovery (part of `libnetwork`) allows all containers and Swarm services to locate each other by name. The only requirement is that they are on the same network.

Every Swarm service and standalone container started with the `--name` flag registers its name and IP with the Docker DNS service.

## Ingress Load Balancing

Swarm supports two publishing modes that makes services accessible outside of the cluster:

- **Ingress mode** (default) - accessible from any node in the Swarm, even nodes not running a service replica.
- **Host mode** - accessible only by hitting nodes running service replicas.

You need to use long form syntax for host mode:

`docker service create -d --name svc1 --publish published=5000,target=80,mode=host nginx`

`published=5000` makes the service available externally via port 5000.

`target=80` makes sure external requests to the published port are mapped back to port 80 on the service replicas.

`mode=host` makes sure external requests only reach the service if they come in via nodes running a service replica.

You'd normally use ingress mode. Behind the scenes, ingress mode uses a layer 4 routing mesh.