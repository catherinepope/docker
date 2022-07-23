# Configuring DeviceMapper

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