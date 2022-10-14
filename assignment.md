## Implementing Services in Kubernetes

This document provides a brief explanation of service implementation in Kubernetes via the *default* implementation concept.

###  Services in Kubernetes
***Services*** are abstracts representing a specific group of service endpoints. Services allow these endpoints to have a stable IP address (sometimes called virtual IP) and port to make reaching them easier. By default, Kubernetes dynamically provides an IP address for your service, which propagates that IP address to all its resources. 

Generally, we can say that services in Kubernetes are divided into two groups—the ones handled by the default implementation concept known as kube-proxy and those implemented through DNS.

### Service Types
You can modify a service type by adjusting the `type` field in the service manifest file. By default, the `type` is empty, indicating a ClusterIP service intended for intra-cluster communication.

We can distinguish the following service types:
* **ClusterIP**: Service designed for intra-cluster communication.
* **NodePort**: Service designed for external communication. When this service, ClusterIP service is also created for handling internal communication. 
* **LoadBalancer**: Service designed for use in cloud-installed clusters. This service uses the cloud load balancer for handling requests to the cluster nodes.

**Manifest File Sample**
```
apiVersion: v1
kind: Service
metadata:
    name: my-service
spec:
    selector:
        app: web-server
        version: 0.1
    type: NodePort
    ports:
    - name:
      nodePort: 31693
      port: 3030
      targetPort: 3000
      protocol: TCP
```
| Field      | Description |
| -----------| ----------- |
| `selector`|Use this field to indicate the service endpoints, i.e., its resources. Make sure this field includes the same group of labels that define the pods explicitly created for this particular service.|
| `type` |Use this field to enter the type of service you want to use. Possible values include `ClusterIP` (default type), `NodePort`, `LoadBalancer`. You can also leave this field empty to use the default service type.|
| `ports`|The `ports` parameter indicates the list of ports for your service. Use the `port` field to specify the service port number and the `targetPort` to specify the corresponding port exposed on the pod. You can use the `protocol` field to specify which of the supported network protocols should be used for communication. Keep in mind that if your ports list includes more than one port, you must use the `name` attribute to denote every separate item on your list. If you plan to use the LoadBalancer or NodePort service types, you can include the `nodePort` field to specify the port for external connections. The default range for `nodePort` is 30000-32767. If you don't specify one, Kubernetes will do that for you.|

### Service Implementation
The implementation of services in Kubernetes is performed via the kube-proxy, which serves as Kubernetes' default service implementation concept. 

Kube-proxy runs on every node in the cluster, making service implementation uniform on every node and, therefore, affects how load balancing is performed. 

> **kube-proxy and load balancing** The default load balancing mechanism in kube-proxy indicates that any service endpoint can become the final target of any request sent to the application. However, you can configure [internal or external traffic policy](https://www.youtube.com/watch?v=1tJZ3wbFngc&ab_channel=CodiLime) to alter that.

#### kube-proxy iptables mode
Iptables is a Linux kernel module with a table-based system for packet operations. The iptables use a netfilter framework for registering kernel module functions. While the network filter uses hook points for that purpose, the iptables introduce the concept of chains that represent the netfilter hook names; This is done to omit directly referring to the netfilter hook names.

When configured to operate in this mode, kube-proxy creates custom chains within iptables and populates these chains with custom rules. 

#### kube-proxy ipvs mode
Intended for large clusters, ipvs mode creates a dedicated interface with the virtual IP of the Kubernetes service is created in every node. This mode is designed for load-balancing purposes and supports many load-balancing algorithms and mechanisms.

#### kube-proxy userspace mode
The userspace mode is a legacy mode. When kube-proxy is configured to operate in this mode, it monitors the control plane for service or service endpoint removals and additions. 

When a service addition is detected, a random port is opened, and traffic is redirected to this port. After that, kube-proxy selects the service backend pod and proxies traffic from the client to the backend.
