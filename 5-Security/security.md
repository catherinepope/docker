# Security

On Linux, Docker leverages most of the common Linux security and workload isolation technologies. These include:

- namespaces
- control groups (cgroups)
- capabilities
- mandatory access control (MAC) systems
- seccomp

**Docker Swarm Mode** is secure by default.

**Docker Content Trust** lets you sign your images and verify the integrity and publish of images you consume.

**Image security scanning** analyses images, detects known vulnerabilities, and provides detailed reports.

**Docker secrets** are stored in the encrypted cluster store, encrypted in-flight when delivered to containers, stored in in-memory filesystems when in use, and operate a least-privilege model.

## Namespaces

Kernel namespaces slice up an operating system so it looks and feels like multiple *isolated* operating systems. This is why you can run multiple web servers on the same OS without experiencing port conflicts.

Each *network namespace* gets its own IP address and full range of ports.

You can run multiple applications, each requiring their own version of a shared library or configuration file. To do this, you run each application inside its own *mount namespace*. This works because each *mount namespace* can have its own isolated copy of any directory on the systme (e.g. /etc, /var, /dev).

Docker on Linux currently utilizes the following kernel namespaces:

- Process ID (pid)
- Network (net)
- Filesystem/mount (mnt)
- Inter-process Communication (ipc)
- User (user)
- UTS (uts)

**Docker containers are an organized collection of namespaces**.

Every container has its own `pid`, `net`, `mnt`, `ipc`, `uts`, and potentially `user` namespace. A container is an organized collection of these namespaces.

### Process ID Namespace

Docker uses the `pid` namespace to provide isolated process trees for each container. Every container gets its own PID 1. PID namespaces also mean that one container cannot see or access the process tree of other containers. Nor can it see or access the process tree of the host on which it's running.

### Network Namespace

Docker uses the `net` namespace to provide each container with its own isolate network stack. This stack includes:

- interfaces
- IP addresses
- port ranges
- routing tables

Every container gets its own `eth0` interface with its own unique IP and range of ports.

### Mount Namespace

Every container gets its own unique isolate root (/) filesystem. This means every container can have its own /etc, /var, /dev constructs. Processes inside a container cannot access the mount namespace of the Linux host or other containers.

### Inter-process Communication Namespace

Docker uses the `ipc` namespace for shared memory access within a container. It also isolate the container from shared memory outside the container.

### User Namespace

Docker lets you use `user` namespaces to map users inside a container to different users on the Linux host. A common example is mapping a container's `root` user to a non-root user on the Linux host.

**A container is a packaged collection of namespaces**.

## Control Groups

Control groups are about setting limits.

Containers are isolated from each other, but all share a common set of OS resources, such as CPU, RAM, network bandwidth, and disk I/O. Cgroups let us set limits on each of these resources so a single container can't consume everything and cause a denial of service (DoS) attack.

## Capabilities

It's a bad idea to run containers as `root`. But non-root users tend to be powerless.

The Linux root user is a long list of *capabilities*. Docker works with *capabilities* so you can run containers as `root`, but strip out all the capabilities you don't need.

This is an example of implementing *least privilege*.

## Mandatory Access Control Systems

Docker works with major Linux MAC technologies, such as AppArmor and SELinux.

## seccomp

Docker uses seccomp, in filter mode, to limit the syscalls a container can make to the host's kernel.

All new containers get a default seccomp profile, configured with sensible defaults. You can customize these profiles, and also pass a flag to Docker so containers can be started without a seccomp profile.

- seccomp
- MAC
- capabilities
- cgroups
- namespaces
- Linux Docker host

## Security in Swarm Mode

By default, Swarm Mode includes:

- Cryptographic node IDs
- TLS for mutual authentication
- Secure join tokens
- CA configuration with automatic certificate rotation
- Encrypted cluster store (config DB)
- Encrypted networks

When you initialize a Swarm with a manager and workers:

- node1 is configured as the first manager of the swarm and also as the root certificate authority (CA).
- the swarm itself is given a cryptographic `clusterID`.
- node1 has issued itself with a client certificate that identifies it as a manager in the swarm.
- certificate rotation is configured with the default value of 90 days.
- a cluster config database is configured and encrypted.
- a set of secure token is created so new managers and workers can join the swarm.

To rotate tokens:

`docker swarm join-token --rotate manager`

Existing managers don't need updating, but you'll need to the new toekn to add new managers.

Join tokens are stored in the cluster store, which is encrypted by default.

### TLS and Mutual Authentication

Every manager and worker that joins the swarm is issued a client certificate. This certificate is used for mutual authentication. It identifies the node, the swarm it's a member of, and the role the node performs in the swarm.

### Configuring CA Settings

To configure the certificate rotation period:

`docker swarm update --cert-expiry 720h`

### The Cluster Store

The cluster store is the brains of the swarm and where cluster config and state are stored. If you're not running in swarm mode, there are many technologies and security features you'll be unable to use.

The store is based on `etcd` and is automatically configured to replicate itself to all managers in the swarm. It is also encrypted by default.

## Signing and Verifying Images with Docker Content Trust

Docker Content Trust (DCT) makes it simple to verify the integrity and publisher of images you pull and run. DCT allows developers to sign images when they're pushed to Docker Hub and other registries. DCT can also be used to provide context, e.g. whether an image has been signed for use in a particular environment (prod or dev).

You need a cryptographic key-pair to sign images:

`docker trust key generate catherine`

To sign an image:

`docker trust sign catherinepope/image:tag`

To inspect the signed image:

`docker trust inspect catherinepope/image:tag`

You can force a Docker host to always sign and verify image push and pull operations by exporting the `DOCKER_CONTENT_TRUST` environment variable with a value of 1.

## Image Scanning

Image scanners inspect images and search for known vulnerabilities.

## Docker Secrets

Secrets are:

- encrypted at rest
- encrypted in-flight
- mounted in containers to in-memory filesystems
- operate under least-privilege model

You can attach secrets to services by specifying the `--secret` flag to the `docker service create` command.
## Transport Layer Security (TLS)

TLS ensures authenticity of the registry endpoint and that traffic to/from the registry is encrypted.

You use TLS (HTTPS) to protect the Docker daemon socket. If you need Docker to be reachable safely through HTTP rather than SSH, you can enable TLS be specifying the `tlsverify` flag and pointing Docker's `tlscacert` flag to a trusted CA certificate.

To configure the Docker engine to use a registry that is not configured with TLS certificates from a trusted CA, pass the `--insecure-registry` flag to the dockerd daemon at runtime. Also, place the certificate in ’/etc/docker/certs.d/dtr.example com/ca.crt’ on all cluster nodes. These two steps will save you from receiving the error ’x509: certificate signed by unknown authority’.

*For the exam, always remember the difference between DCT and TLS. The exam tries to confuse you about them.*

## Mutually Authenticated Transport Layer Security (MTLS)

- Both participants in communication exchange certificates and all communication is *authenticated* and *encrypted*.
- When a swarm is initialized, a root certificate authority (CA) is create, which is used to generate certificates for all nodes as they join the cluster.
- Worker and manager tokens are generated using the CA and are used to join new nodes to the cluster.
- Used for all cluster-level communication between swarm nodes.
- Enabled by default, you don't need to do anything to set it up.
