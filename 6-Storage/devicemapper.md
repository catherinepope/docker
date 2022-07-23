Storage drivers are sometimes known as *graph drivers*. The appropriate storage driver often depends on your OS:

- overlay2: current Ubuntu and CentOS
- aufs: Ubuntu 14.04 and older
- devicemaper: CentOS 7 and earlier.

## Storage Models

Persistent data can be managed using several storage models.

Filesystem storage:

- Data stored in form of a file system.
- Used by overlay2 and aufs
- Efficient use of memory
- Inefficient with write-heavy workloads.

Block storage:

- Stores data in blocks.
- Used by devicemapper.
- Efficient with write-heavy workloads.

Object storage:

- Stores data in an external object-based stored.
- Application must be designed to use object-based storage.
- Flexible and scalable.


## Configuring DeviceMapper

DeviceMapper is one of the Docker storage drivers available for some Linux distributions.

You can customize your DeviceMapper configuration using the daemon config file.

DeviceMapper supports two modes:

**loop-lvm** mode:

- Loopback mechanism simulates an additional physical disk using files on the local disk.
- Minimal setup, doesn't require an additional storage device.
- Bad performance, only use for testing.

**direct-lvm** mode:

- Stores data on a separate device.
- Requires an additional storage device.
- Good performance, use for production.