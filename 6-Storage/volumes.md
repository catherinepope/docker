# Storage and Volumes

There are two main categories of data:

- persistent
- non-persistent

Every Docker container gets its own non-persistent storage. This is created automatically as is tightly coupled to the container's lifecycle.

To persist data, a container needs to store it in a volume. Volumes are separate objects whose lifecycles are decoupled from containers.

## Non-Persistent Data

Every Docker container is created by adding a thin read-write layer on top of the read-only image on which it's based. The writable layers exists in the filesystem of the Docker host: `/var/lib/docker/<storage-driver>`.

Any data written to this layer is deleted when the container is deleted.

This writable layers of local storage is managed on every Docker host by a storage driver:

- **Red Hat Enterprise Linux**: Use the `overlay2` driver with modern versions, or `devicemapper` driver with older versions.
- **Ubuntu**: Use the `overlay2` or `aufs` drivers. 
- **SUSE Linux Enterprise Server**: Use the `btrfs` storage driver.
- **Windows**: Has only one default driver.

## Persistent Data

Volumes are the recommended way to persist data in containers.

- Create a volume.
- Create a container and mount the volume into it.
- The volume is mounted into a directory in the container's filesystem.
- Anything written to that directory is stored in the volume.
- If you delete the container, the volume and its data still exist.

To create a volume:

`docker volume create <volume-name>`

By default, Docker creates new volumes with the built-in `local` driver. As the name suggests, volumes created with the `local` driver are available only to containers on the same node as the volume. You can use the `-d` flag to specify a different driver.

Third-party volume drivers are available as plugins.

Use `docker volume inspect` to see what driver it's using and where the volume exists.

All volumes created with the `local` driver get their own directory under `/var/lib/docker/volumes` on Linux. This means you can see them in your Docker host's filesystem.

To remove a volume, use `docker volume rm`. 

To delete any unmounted volumes, use `docker volume prune`.

You can also deploy volumes via Dockerfiles using the `VOLUME` instruction: `VOLUME <container-mount-point>`. You cannot specify a directory on the host when defining a volume in a Dockerfile. This is because host directories differ according to the OS on which your Docker host is running. Consequently, defining a volume in a Dockerfile requires you to specify host directories at deploy-time.

## Mounting a Volume

`docker container run -dit --name voltainer --mount source=bizvol,target=/vol alpine`

If you specify a volume that doesn't exist, Docker creates if for you.

## Sharing Storage Across Cluster Nodes

For this to work, you need a volumes driver plugin that works with the external storage system.

Once the plugin is registered, you can create new volumes from the storage system using `docker volume create` with the `-d` flag.