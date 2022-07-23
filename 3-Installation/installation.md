# Installation and Configuration

There are two Docker editions:

- Community Edition (CE, entirely free)
- Enterprise Edition (EE, not free)

## Docker Enterprise Edition

Docker Enterprise Edition (EE) is now rebranded as Mirtantis Kubernetes Engine (MKE).

Docker EE has three components:

- Docker EE
- UCP (Universal Control Plane)
- DTR (Docker Trusted Registry)

You interact with the UCP, rather than directly with Docker EE.

The UCP is a container that you run from an image.

You cannot install the DTR on the same node as the UCP. Therefore, your swarm must have more than one node.

Fortunately, the (very tricky) installation steps aren't covered by the exam!

### Universal Control Plane (UCP)

You can switch between orchestrators in the UI.

If you create a secret in your swarm, you can still access it using Kubernetes.

#### Sizing Requirements

- Minimum 8Gb memory and 2 CPUs for manager modes.
- Recommended 16Gb memory and 4 CPUs for manager modes.
- Mininum 4Gb memory for worker nodes.


## Configuration of Logging Drivers

By default, Docker uses the json-file logging driver, which caches container logs as JSON internally.

Docker can also use other drivers, such as splunk and journald.

To configure the logging driver, run the following command:

```
docker run -it --log-driver <log driver> <image>
```

To fetch the driver type, run:

```
docker inspect -f '{{.HostConfig.LogConfig.Type}}' <CONTAINER>
```

## Backing Up UCP and DTR

1. Back up the Docker Swarm
2. Back up the UCP.
3. Back up the DTR Images.
4. Back up DTR metadata.

