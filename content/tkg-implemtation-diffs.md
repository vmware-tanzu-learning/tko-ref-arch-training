# Overview

Tanzu Kubernetes Grid builds on trusted upstream and community projects and delivers a Kubernetes platform that is engineered and supported by VMware, so that you do not have to build your Kubernetes environment by yourself. In addition to Kubernetes binaries that are tested, signed, and supported by VMware, Tanzu Kubernetes Grid provides the services such as networking, authentication, ingress control, and logging that a production Kubernetes environment requires.

# Comparing Tanzu Kubernetes Grid Implementations

Something to be aware of is that there are different implementations of Tanzu Kubernetes Grid. The most common implementations are:

* Tanzu Kubernetes Grid Service (TKGS)
* Tanzu Kubernetes Grid multi-cloud (TKGm)

At their most basic all of the Tanzu Kubernetes Grid implementations provision and manage the lifecycle of Tanzu Kubernetes clusters and provide a platform for running containerized Kubernetes workloads. The way that they do this however is slightly different based on the features and functionality that need to be provided to a consumer. The major thing to be aware of is the supported infrastructure the platform can be deployed on. TKGS is provided as part of vSphere with Tanzu and is only supported on vSphere based infrastructure. TKGm however can be deployed on vSphere, AWS and Azure based infrastructure.

Both have the concept of a Management Cluster which can be used to manage and deploy Kubernetes clusters but in the case of TKGS this is a highly opinionated VMware variant named the Supervisor cluster. The Supervisor cluster is not a conformant Kubernetes cluster by design and makes use of Kubernetes and its inherent functionality to enhance vSphere and add a number of additional features such as:

* vSphere Namespaces (Basic Multi-Tenancy)
* vSphere PODs (Ability to run Kubernetes Pods natively on ESXi hypervisor alongside virtual machines)
* vSphere SSO integration (Cluster authentication via configured vSphere mechanisms)
* vSphere Registry Service (Embedded Harbor instance)

The Supervisor cluster is deployed by enabling Workload Management from within vSphere. Workload Management provides tight integration with the underlying vSphere storage and networking stack such as vSAN and NSX-T.

In comparison TKGm makes use of the upstream [Cluster API](https://cluster-api.sigs.k8s.io/introduction.html) Kubernetes sub-project and can be used to deploy a conformant Kubernetes cluster to your choice of supported infrastructure. The management cluster is deployed as an initial step after which workload clusters can be deployed. Due to the fact that TKGm is based on upstream Kubernetes it is much more customizable to advanced use cases such as those for Telco for instance.

## Detailed Comparison

A more detailed comparison between the 2 implementations is included in the sections below:

### Infrastructure

|  | TKGS | TGKm |
|---|---|---|
| Supported Infrastructure | vSphere, VMC on AWS | vSphere, VMC on AWS, AWS, Azure, Docker, Azure VMware solution |
| Minimum Hosts | Requires a minimum of 3 ESXi hosts. This caters by default to high availability of the Supervisor cluster | Can be deployed with a single host. Availability considerations in this case need to be understood |
| Supported OS | Photon OS and Ubuntu | Photon OS for vSphere and Ubuntu for vSphere, AWS and Azure. Amazon Linux for AWS |
| Bring your own OS | Limited by opinionated Supervisor Cluster | Can bring your own OS built using image builder |
| Windows Container Support | No | Yes |

### Networking

|  | TKGS | TGKm |
|---|---|---|
| CNI | * Antrea or Calico for Tanzu Kubernetes clusters (TKC)<br> * NSX-T for vSphere Pod Service<br> * Antrea only for Supervisor cluster<br> * NCP if NSX-T is deployed and vSphere pods are being used | * Antrea or Calico for Management and Workload clusters<br> * Multus CNI for Workload clusters if secondary interface is required for Pods |
| Load Balancer - Control Plane | NSX-ALB, NSX-T, HAProxy | NSX-ALB, Kube-vip for vSphere, AWS ELB for AWS, Azure Load Balancer for Azure |
| Load Balancer - Pod L4 (Service type = Loadbalancer) | NSX-ALB, NSX-T, HAProxy | NSX-ALB, DIY like metal-lb, HAPRoxy or F5 for vSphere, AWS ELB for AWS, Azure Load Balancer for Azure |
| Ingress Controller | Contour, NSX ALB via AKO | Contour, NSX-ALB via AKO |
| IPv6 supported | No | Yes with some limitations |

### Kubernetes

|  | TKGS | TGKm |
|---|---|---|
| Management Cluster | Management Cluster is an opinionated VMware variant named the Supervisor cluster. This is deployed by enabling Workload Management from within vSphere | Management cluster needs to be deployed on infrastructure of choice as a first step |
| Tuning and Customization | Limited by opinionated Supervisor Cluster. CNI and CSI are predefined | Customizable control and tuning over api-server, kubelet flags, CNI and CSI |
| Workload cluster autoscaling | No | Yes |
| Authentication | Tied to vSphere SSO and its integration with well used authentication mechanisms such as LDAP. Requires minimal well known configuration | Authentication is optional but can be integrated with OIDC/LDAP. Requires deeper knowledge for integration. OIDC support via Pinniped. LDAP support via Pinniped and Dex |

### Operations

|  | TKGS | TGKm |
|---|---|---|
| Command Line Interface | Requires kubectl vSphere plugin for authentication and usage | Native kubectl can be used |
| Cluster Upgrades | TKG upgrades are tied to vSphere upgrades | Kubernetes and TKG upgrades can be more flexible and current |

### Extensibility

|  | TKGS | TGKm |
|---|---|---|
| Package Management | Not supported | Supported with a repository of value added tools required to make a Kubernetes cluster fully production ready such as Harbor, Contour, external-dns etc |
| Observability Extensions | Fluent Bit, Prometheus & Grafana, Tanzu Observability | Fluent Bit, Prometheus & Grafana, Tanzu Observability |
| Backup | Velero for Workload clusters | Velero for Management and Workload clusters |

### Value Added Features

| TKGS | TGKm |
| --- | --- |
| PodVM's - The ability to run pods on native ESXi hypervisor  | Aligned to upstream open source Kubernetes and Cluster API releases and runs validation via CNCF conformance inspection |
| Tight NSX-T integration and automation. For example:  T1 routers and segments are automatically created for new vSphere namespaces as well as the automatic creation of service type LoadBalancer in NSX-T | Any compatible datastore can be used as provided by the infra provider |
| Tight vSAN integration and automation and Cloud Native Storage via pvCSI which is compatible with any vSphere datastore |
| Harbor - Internal shared Harbor deployment when using NSX-T as it makes use of PodVMs
| vSphere Namespaces - Provide limited multi tenancy capabilities |
| VM Service - The ability for developers to deploy Virtual Machines using native Kubernetes yaml. |
| GPU Support |

## Additional Info

[VMware Tanzu Kubernetes Grid Documentation](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/index.html#what-is-tanzu-kubernetes-grid-0)
