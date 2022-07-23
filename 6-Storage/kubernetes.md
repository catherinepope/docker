# Storage in Kubernetes
The storage in Kubernetes is defined over several phases. 

The first phase is to have the physical storage connected to the cluster and define its drivers/plugins. The plugin is called the provisioner/Container Storage Interface (CSI), and the provider affords it with the storage disks.

The second phase is to make the Kubernetes cluster aware of these storage disks. To do that, you define a persistent volume (PV) YML file. 

The third phase is using the volumes. The pods or the deployments do that by issuing a ticket to register the volume; we do that by creating a new object called the persistent volume claim (PVC). We create it also by writing a PVC YML file.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <PV name>
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: <sc name>
  capacity:
    storage: <storage size>
  persistentVolumeReclaimPolicy: Retain
  <provisioner parameters>

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <PVC name>
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: <same sc name as in the PV object>
  resources:
    requests:
      storage: <same storage size as in the PV object>
```

For scaling storage, Kubernetes offer another object called *storage class (SC)*. Storage classes create PVs dynamically. Therefore, we don't need to define the PV individually. It's similar to deployments in Kubernetes that create the pods.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: <SC name>

<provisioner parameters>
```