# NGF-master
### https://devopscube.com/kubernetes-gateway-api/
### https://devopscube.com/istio-ingress-kubernetes-gateway-api/

## Introduction

Kubernetes has become the foundation for cloud-native applications. However, managing and routing traffic within clusters remains a challenging issue. The traditional Ingress resource, though helpful in exposing services, has shown limitations. Its loosely defined specifications often cause controller-specific behaviors, complicated annotations, and hinder portability across different environments. These challenges become even more apparent as organizations scale their microservices architectures. Ingress was designed primarily for basic service exposure and routing. While it can be extended with annotations or custom controllers, it lacks first-class support for advanced deployment patterns such as canary or blue-green releases. This forces teams to rely on add-ons or vendor-specific features, which adds complexity and reduces portability.

To overcome the limitations of traditional ingress controllers better, the Kubernetes community has introduced the Gateway API. This is a new, forward-looking, standards-based approach to service networking. Unlike the more rigid Ingress, the Gateway API provides greater flexibility, role-specific functionalities, and a comprehensive set of features. It encourages collaboration among platform engineers, developers, and security teams by supporting advanced capabilities such as TLS offloading, traffic splitting, and smooth integration with service meshes.

In short, the Gateway API is a paradigm shift in traffic management.

Hyperlinks: 
🚀 Deploy
⚙️ Config
🧪 Testing
🛡️ Security
🧵 Logs
🔧 Debug
📘 Docs

## NGINX Gateway Fabric - F5 NGINX Implementaion of Gateway API
NGINX Gateway Fabric provides an implementation of the Gateway API using NGINX as the data plane. The goal of the project is to implement the core Gateway APIs needed to configure an HTTP or TCP/UDP load balancer, reverse proxy, or API gateway for Kubernetes applications.

Built on the Gateway API standard, NGINX Gateway Fabric offers a production-ready solution. It combines the robustness and performance of NGINX with the extensibility of this new standard. It provides consistent traffic management, observability, and security across Kubernetes clusters. Additionally, its integration with NGINX One Console enables centralized control and monitoring in distributed environments.

For a list of supported Gateway API resources and features, see the [Gateway API Compatibility](https://docs.nginx.com/nginx-gateway-fabric/overview/gateway-api-compatibility/) documentation.
---
### Design
---
NGINX Gateway Fabric separates the control plane and data plane into distinct deployments. The control plane interacts with the Kubernetes API, watching for Gateway API resources.

![diagram](./images/picture1.png)

When a new Gateway resource is provisioned, it dynamically creates and manages a corresponding NGINX data plane Deployment and Service.

Each NGINX data plane pod consists of an NGINX container integrated with NGINX Agent. The control plane translates Gateway API resources into NGINX configurations and sends these configurations to the agent to ensure consistent traffic management.

This design enables centralized management of multiple Gateways while ensuring that each NGINX instance stays aligned with the cluster’s current configuration.


For more information, see the [Gateway architecture](https://docs.nginx.com/nginx-gateway-fabric/overview/gateway-architecture/) topic.

### How Is NGINX Gateway Fabric Different from NGINX Ingress Controller?
---
F5 NGINX Ingress Controller implements the Ingress API specification to deliver core functionality, using custom annotations, CRDs, and NGINX Ingress resources for expanded capabilities. NGINX Gateway Fabric conforms to the Gateway API specification, simplifies implementation, and aligns better with the organizational roles that deal with service networking configurations.

The following table compares the key high‑level features of the standard Ingress API, NGINX Ingress Controller with CRDs, and Gateway API to illustrate their capabilities.

![compare-apis](./images/compare-apis-ingress-gwapi.png)

---
### Installation
We will make use of killercoda environment. 
```mermaid
graph TD
  A[Killercoda login] --> B[Choose a Kubernetes Playground]
  B --> C[Explore Environment]
  C --> D[Deploy NGINX Gateway Fabric]
  D --> E[Test Gateway API Use Cases]
```


```mermaid
sequenceDiagram
  Client->>NGINX: Request
  NGINX->>Backend: Proxy
  Backend-->>NGINX: Response
  NGINX-->>Client: 200 OK
```  
Login to killercoda.com
![killercoda-logn](./images/00-killercoda-login.png)

Click Playgrounds
![killercoda-pgs](./images/01-killercoda-pg.png)

Select a Playground 
![killercoda-pg](./images/02-killercoda-nodes.png)

Start using killercoda
![killercoda-start](./images/03-killercoda-start.png)
HELM

Explore killercoda Environment
```sh
kubectl get nodes -owide
kubectl get ns
kubectl get pods -A
kubectl api-resources
kubectl get crd

...
```

<details>
<summary> Click to View Example Output
![killercoda-explore](./images/04-killercoda-explore-example.png)
</details>

### What the HELM!
brief on helm

---
asdfadsf
---
# Further Reading
---
[GitHub](https://github.com/nginx/nginx-gateway-fabric)

[Kubernetes Networking: Moving from Ingress Controller to the Gateway API](https://blog.nginx.org/blog/kubernetes-networking-ingress-controller-to-gateway-api)

[Modern Deployment and Security Strategies for Kubernetes with NGINX Gateway Fabric](https://community.f5.com/kb/technicalarticles/modern-deployment-and-security-strategies-for-kubernetes-with-nginx-gateway-fabr/343305)

## faq
### Is NGINX Gateway Fabric Going to Replace NGINX Ingress Controller?
NGINX Gateway Fabric is not replacing NGINX Ingress Controller. Rather, it is an emerging technology based on the first generally available release of the Gateway API specification. NGINX Ingress Controller is a mature, stable technology used in production by many customers. It can be tailored for specific use cases through custom annotations and CRDs. For example, to implement the role‑based approach, NGINX Ingress Controller uses NGINX Ingress resources, including VirtualServer, VirtualServerRoute, TransportServer, and Policy.

We don’t expect NGINX Gateway Fabric to replace NGINX Ingress Controller any time soon – if that transition does happen, it’s likely to be years away. NGINX Ingress Controller will continue to play a critical role in managing north‑south network traffic for a diverse variety of environments and use cases, including load balancing, traffic limiting, traffic splitting and security.

### Is NGINX Gateway Fabric an API Gateway?
While it’s reasonable to think something named “Gateway API” is an “API gateway”, this is not the case. As discussed in How Do I Choose? API Gateway vs. Ingress Controller vs. Service Mesh, “API gateway” describes a set of use cases that can be implemented via different types of proxies – most commonly an ADC or load balancer and reverse proxy, and increasingly an Ingress controller or service mesh. That said, much like NGINX Ingress Controller, NGINX Gateway Fabric can be used for API gateway use cases, including routing requests to specific microservices, implementing traffic policies, and enabling canary and blue‑green deployments. This release is focused on processing HTTP/HTTPS traffic. More protocols and use cases are planned for future releases.

### How Do I Get Started with NGINX One?
Ready to try this exciting new technology? Get the release of NGINX Gateway Fabric. For deployment instructions, see the README.

For detailed information on the Gateway API specifications, refer to the Kubernetes Gateway API documentation.

```mermaid
We encourage you to submit feedback, feature requests, use cases, and any other suggestions so that we can help you solve your challenges and succeed. Please share your feedback at our GitHub repo.
```