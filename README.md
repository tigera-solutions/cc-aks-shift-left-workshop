# Microsoft Azure: Hands-on AKS workshop </br> Shift-left security with Vulnerability Management in AKS

## Welcome

In this AKS-focused workshop, you will work with Microsoft Azure and Calico Cloud to learn how to design and deploy best practices to secure your Kubernetes environment at build,deploy and runtime -  preventing, detecting and building a security moat around your workloads to protect from container and network-based attacks as early as possible in the development and early runtime phases.

Cloud-native applications require a modern approach on zero-trust principles identity-based access, least privilege access, and proactively putting in the guardrails as early in the development process as possible.

Calico Cloud enables Image Assurance to establish a security posture around container image vulnerability management during build, deploy and runtime while also providing an IDS/IPS via DPI (Deep Packet Inspection) and a Network Policy recommender to setup a baseline zerotrust network policy posture for new and existing workloads along with Wireguard encryption for the inter-node pod-to-pod traffic to encourage a shift-left security mindset and implement best practices early and often.

You will come away from this workshop with an understanding of how others in your industry are securing and observing cloud-native applications in Microsoft Azure, along with best practices that you can implement in your organization.

## Time Requirements

The estimated time to complete this workshop is 90-120 minutes.

## Target Audience

- Cloud Professionals
- DevSecOps Professional
- Site Reliability Engineers (SRE)
- Solutions Architects
- Anyone interested in Calico Cloud :)

## Learning Objectives

Learn how to build a security moat around your workloads by:

- Scanning container images and blocking deployment based on your security criteria during build time.
- Enabling in-cluster image scanning to proactively and iteratively allow clusters to always scan images and workloads in the background
- Implementing runtime security with IDS/IPS using DPI and then using the network policy recommender to develop a zero-trust default-deny approach
- Encrypting inter-node pod-to-pod traffic in a cluster as best practice using Wireguard
- Getting visibility inside your Kubernetes cluster traffic to troubleshoot and improve security posture

## Modules

This workshop is organized in sequential modules. One module will build up on top of the previous module, so please, follow the order as proposed below.

Module 1 - [Getting Started](modules/module-1-getting-started.md)</br>
Module 2 - [Deploy an AKS cluster](modules/module-2-deploy-aks.md)</br>
Module 3 - [Connect the cluster to Calico Cloud](modules/module-3-connect-calicocloud.md)</br>
Module 4 - [Scan Container Images](modules/module-4-scan-images.md)</br>
Module 5 - [Calico Cloud Admission Controller](modules/module-5-admission-controller.md)</br>
Module 6 - [In-cluster Image Scanning](modules/module-6-inclusterscanning.md)</br>
Module 7 - [Runtime security with IDS/IPS using Deep Packet Inspection](modules/module-7-runtimesec.md)</br>
Module 8 - [Zero-trust access control using Network Policy Recommender](modules/module-8-zerotrust.md)</br>
Module 9 - [Enabling End to End Encryption with WireGuard](modules/module-9-encryption.md)</br>
Module 10 - [Traffic visualization inside your Kubernetes Cluster](modules/module-10-visibility.md)</br>
Module 11 - [Clean up](modules/module-11-cleanup.md)</br>

---

### Useful links

- [Project Calico](https://www.tigera.io/project-calico/)
- [Calico Academy - Get Calico Certified!](https://academy.tigera.io/)
- [O’REILLY EBOOK: Kubernetes security and observability](https://www.tigera.io/lp/kubernetes-security-and-observability-ebook)
- [Calico Users - Slack](https://slack.projectcalico.org/)

**Follow us on social media**

- [LinkedIn](https://www.linkedin.com/company/tigera/)
- [Twitter](https://twitter.com/tigeraio)
- [YouTube](https://www.youtube.com/channel/UC8uN3yhpeBeerGNwDiQbcgw/)
- [Slack](https://calicousers.slack.com/)
- [Github](https://github.com/tigera-solutions/)
- [Discuss](https://discuss.projectcalico.tigera.io/)

> **Note**: The workshop provides examples and sample code as instructional content for you to consume. These examples will help you understand how to configure Calico Cloud and build a functional solution. Please note that these examples are not suitable for use in production environments.