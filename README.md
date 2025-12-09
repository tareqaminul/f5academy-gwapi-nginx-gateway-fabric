# NGINX Gateway Fabric Lab Guide

A comprehensive hands-on guide to understanding and implementing the Kubernetes Gateway API with NGINX Gateway Fabric.

[![NGINX Gateway Fabric](https://img.shields.io/badge/NGINX-Gateway%20Fabric-009639?logo=nginx)](https://github.com/nginx/nginx-gateway-fabric)
[![Gateway API](https://img.shields.io/badge/Gateway%20API-Compatible-326CE5?logo=kubernetes)](https://gateway-api.sigs.k8s.io/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

## 📚 What You'll Learn

By the end of this guide, you will have mastered:

- ✅ Understanding the Kubernetes Gateway API and its advantages over Ingress
- ✅ Key concepts and architecture of the Gateway API
- ✅ Practical implementation with NGINX Gateway Fabric
- ✅ Working with Gateway API resources (GatewayClass, Gateway, HTTPRoute)
- ✅ Implementing path-based routing and traffic management
- ✅ Real-world deployment patterns and best practices

---

## 📖 Table of Contents

- [Introduction](#introduction)
- [Architecture](#architecture)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Testing Use Cases](#testing-use-cases)
- [FAQ](#faq)
- [Further Reading](#further-reading)

---


## Introduction

### The Gateway API: A Paradigm Shift in Kubernetes Traffic Management

Kubernetes has become the foundation for cloud-native applications, but managing and routing traffic within clusters remains challenging. The traditional Ingress resource, while helpful for exposing services, has shown significant limitations:

- **Loosely defined specifications** leading to controller-specific behaviors
- **Annotation overload** making configurations complex and unportable
- **Limited support** for advanced deployment patterns (canary, blue-green releases)
- **Vendor lock-in** through proprietary extensions and features

### Enter the Gateway API

The Kubernetes Gateway API is a new, standards-based approach to service networking that addresses these limitations by providing:

- **Greater flexibility** with role-oriented design
- **Comprehensive feature set** for modern traffic management
- **Built-in support** for TLS offloading, traffic splitting, and service mesh integration
- **Clear separation** between platform engineers, developers, and security teams

> **💡 Key Concept**: Just like Ingress controllers, the Gateway API separates concerns:
> 
> - **Gateway API Resources**: Define routing rules (Gateway, HTTPRoute, etc.)
> - **Gateway API Controller**: Handles actual traffic routing (not built into Kubernetes)
> 
> You must install a Gateway API controller implementation, such as NGINX Gateway Fabric, to process these resources.

---

## NGINX Gateway Fabric - F5 NGINX Implementaion of Gateway API
NGINX Gateway Fabric provides an implementation of the Gateway API using NGINX as the data plane. The goal of the project is to implement the core Gateway APIs needed to configure an HTTP or TCP/UDP load balancer, reverse proxy, or API gateway for Kubernetes applications.

Built on the Gateway API standard, NGINX Gateway Fabric offers a production-ready solution. It combines the robustness and performance of NGINX with the extensibility of this new standard. It provides consistent traffic management, observability, and security across Kubernetes clusters. Additionally, its integration with NGINX One Console enables centralized control and monitoring in distributed environments.

For a list of supported Gateway API resources and features, see the [Gateway API Compatibility](https://docs.nginx.com/nginx-gateway-fabric/overview/gateway-api-compatibility/) documentation.
---


## Architecture

### NGINX Gateway Fabric Overview

[NGINX Gateway Fabric](https://github.com/nginx/nginx-gateway-fabric) is F5 NGINX's production-ready implementation of the Gateway API standard. It leverages NGINX as the data plane to provide:

- High-performance HTTP and TCP/UDP load balancing
- Reverse proxy and API gateway capabilities
- Centralized management via NGINX One Console
- Full observability and security features

For a complete list of supported resources and features, see the [Gateway API Compatibility](https://docs.nginx.com/nginx-gateway-fabric/overview/gateway-api-compatibility/) documentation.

### Design Architecture

NGINX Gateway Fabric uses a split-plane architecture with two controller types:

#### Control Plane Controller
- Deployed when you install NGINX Gateway Fabric
- Watches Gateway API custom resources (Gateway, HTTPRoute, TLSRoute, etc.)
- Translates Gateway API resources into NGINX configurations
- Manages the lifecycle of data plane deployments

#### Data Plane Controller
- Dynamically created when a Gateway resource is provisioned
- Consists of NGINX container with NGINX Agent
- Handles actual traffic routing to services
- Each Gateway gets its own isolated data plane deployment

```mermaid
graph TB
    A[Client Request] --> B[Gateway Service]
    B --> C[Data Plane Pod]
    C --> D[NGINX Container]
    C --> E[NGINX Agent]
    F[Control Plane Controller] -->|Watches| G[Gateway API Resources]
    F -->|Creates Config| E
    F -->|Provisions| C
    D --> H[Backend Services]
```
----------------
---
### Design-remove
---

Nginx Gateway Fabric(NGF) consists of two types of controllers.
- **Control Plane Controller**
- **Data Plane Controller**

It separates the control plane and data plane into two distinct deployments. The control plane interacts with the Kubernetes API, watching for Gateway API resources.

**Control Plane Controller**
- When we install the Nginx Gateway Fabric on Kubernetes, the Control plane controller will be deployed.

- The job of this controller is to watch its custom resources, such as Gateway, HTTPRoute, TLSRoute, etc.

- When we create a custom resource, the control plane controller will get the required resources and create the object.

**Date Plane Controller**
- When we create a Gateway Custom Resource, the control plane controller will dynamically create a Data plane controller with the given information on the Gateway.

- The data plane controller actually routes the traffic to the specific services.

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
## Installation
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

Example Output
![killercoda-explore](./images/04-killercoda-explore-example.png)

### Deploy Using HELM

Install the Gateway API resources
```bash
kubectl kustomize "https://github.com/nginx/nginx-gateway-fabric/config/crd/gateway-api/standard?ref=v2.2.1" | kubectl apply -f -
```

Install NGINX Gateway Fabric from OCI Registry
```bash
helm install ngf oci://ghcr.io/nginx/charts/nginx-gateway-fabric --create-namespace -n nginx-gateway
```
<details>
<summary>What the HELM!</summary>
brief helm
</details>

### Example application
We deployed the NGINX implementation of Gateway API and now, it is ready to process the API requests relevant to configuring L4 and L7 routing in Kubernetes. Pls check [here](https://gateway-api.sigs.k8s.io/concepts/api-overview/), for a quick overview! But before that, we need an example application with multiple routes to which the NGF would route traffic to. 

The application we are going to use in this guide is a simple cafe application comprised of two services - coffee and tea. The initial objective is to route all traffic to hostname **cafe.example.com** to one of these services based on URI Parameters **/coffee** OR **/tea**.

![example-app](./images/coffee-tea-routing.jpg)

None of the services is accessible directly from outside the cluster. We want to expose this application on the hostname "cafe.example.com" so that clients outside the cluster can access it and 

Let us deploy the example application: 

## Test Gateway Use Cases

```yaml
cat <<EOF > cafe.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: coffee
spec:
  replicas: 1
  selector:
    matchLabels:
      app: coffee
  template:
    metadata:
      labels:
        app: coffee
    spec:
      containers:
      - name: coffee
        image: nginxdemos/nginx-hello:plain-text
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: coffee
spec:
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: coffee
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tea
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tea
  template:
    metadata:
      labels:
        app: tea
    spec:
      containers:
      - name: tea
        image: nginxdemos/nginx-hello:plain-text
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: tea
spec:
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: tea
EOF
kubectl apply -f cafe.yaml
```
Verify
```bash
kubectl get pods,svc
```

Your output should include coffee and tea pods and services::
```txt
$ kubectl get pods,svc
NAME                          READY   STATUS    RESTARTS   AGE
pod/coffee-5b9c74f9d9-7n22w   1/1     Running   0          98s
pod/tea-859766c68c-jmjxl      1/1     Running   0          98s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/coffee       ClusterIP   10.100.139.53   <none>        80/TCP    98s
service/tea          ClusterIP   10.103.46.146   <none>        80/TCP    98s
```
The services are internal  create two Gateway API resources: a gateway and an HTTPRoute.

Using these resources we will configure a simple routing rule to match all HTTP traffic with the hostname "cafe.example.com" and route it to the coffee service.

Create Gateway and HTTPRoute resources
Run the following command to create the file gateway.yaml, which is then used to deploy a Gateway to your cluster:
```bash
cat <<EOF > gateway.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: gateway
spec:
  gatewayClassName: nginx
  listeners:
  - name: http
    port: 80
    protocol: HTTP
    hostname: "*.example.com"
EOF
kubectl apply -f gateway.yaml
```
```txt
gateway.gateway.networking.k8s.io/gateway created
```
Verify that the NGINX deployment has been provisioned:
```bash
kubectl -n default get pods
```
```txt
NAME                             READY   STATUS    RESTARTS   AGE
coffee-676c9f8944-k2bmd          1/1     Running   0          31s
gateway-nginx-66b5d78f8f-4fmtb   1/1     Running   0          13s
tea-6fbfdcb95d-9lhbj             1/1     Running   0          31s
```
Test
```bash
curl  http://localhost:32659/coffee -H "Host: cafe.example.com"
```

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

```text
We encourage you to submit feedback, feature requests, use cases, and any other suggestions so that we can help you solve your challenges and succeed. Please share your feedback at our GitHub repo.
```
