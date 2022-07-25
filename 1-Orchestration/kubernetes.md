# Kubernetes

You need to define a readiness probe on a Kubernetes Pod template definition if you want your container to be able to take itself down for maintenance.

You should use a *job* to create Pods that are expected to terminate.

To use a secret as an environment variable in the Pod definition, use `env[].valueFrom.secretKeyRef`.

## Manifest Files

1.  **apiversion:** Indicates the version of the manifest file.
    
2. **kind:** Specifies the object type, for example, pod, deployment, service, namespace, persistent volume, persistent volume claim, or storage class.
    
3. **metadata:** This key is crucial because it defines the labels. The name of the app is one of the labels. We can define more than one label for the object. Also, in metadata, we define the namespace name.
    
4. **spec:** This is where the action is. In this top key, we define the replicas, the selector, and the template. Inside the template, we can define the image where the container will be crafted from, ports, and any other specifications to run the application correctly.

We use namespaces to allow several projects to share the same Kubernetes cluster. Therefore, Kubernetes needs namespaces to know the interconnected apps that are communicating with each other and isolate them from other projects. In other words, namespaces divide the cluster into interconnected sub-clusters.

Generally, deployments are responsible for self-healing and scalability. Deployments manage replica sets, and replica sets manage pods.

A Kubernetes service uses labels as nametags to identify pods and it can query these labels for service discovering and load-balancing. 

You can reference a a secret from a Pod through the kubelet, when pulling images for the Pod.

### Kubernetes Networking

Kubernetes uses a service to provide a static interface to deployments where the pods are running. Services provide three main criteria:

- Stable IP
- DNS
- Port

There are three types of service:

- **ClusterIP**: provides the service inside the cluster.
- **NodePort**: makes the service accessible from outside the cluster.
- **LoadBalancer**: integrates the service with cloud providers.

- A ClusterIP service has a stable IP adress and port that is accessible only from within the cluster. It registers a DNS name, a virtual IP, and a port with the cluster DNS.
- A NodePort service builds on top of the ClusterIP and enables access from outside of the cluster. It adds a new port called NodePort to be used to reach the service from outside the cluster.
- A LoadBalancer builds on top of the NodePort and integrates with load balancers from your cloud provider.

```
apiVersion:  v1
kind:  Service
metadata:
  name:  app
spec:
  type:  clusterIP
  selector:
    app:  nginx
    ports:
    - port:  8080
      targetPort:  80
    -port:  4515
     targetPort:  443
```
