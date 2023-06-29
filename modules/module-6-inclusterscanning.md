# Module 6 - In-Cluster Image Scanning

Calico Cloud also includes an in-cluster image scanner for strengthening the security posture of the cluster for use-cases where the image scanner build/deploy process might get bypassed by a team with escalated privileges or is a good starting point to understand the security posture of a cluster that already has running workloads to allow teams to focus their efforts on the significant vulnerable images. 

## Pre-requisites

The cluster must be:

- Using containerd as the container runtime.
- Containerd must be using overlayfs or native file system snapshotter.

Our standard AKS cluster fulfils both of these pre-requisites, the container runtime can be verified via a ```kubectl get nodes -o wide``` command

```bash                                                                                                                                                                                                     ☸️  aks-shift-left-workshop22:24:05
NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-24664026-vmss000000   Ready    agent   7h15m   v1.25.6   10.224.0.103   <none>        Ubuntu 22.04.2 LTS   5.15.0-1040-azure   containerd://1.7.1+azure-1
aks-nodepool1-24664026-vmss000001   Ready    agent   7h15m   v1.25.6   10.224.0.4     <none>        Ubuntu 22.04.2 LTS   5.15.0-1040-azure   containerd://1.7.1+azure-1
```

## Enable the in-cluster scanner

- Modify the imageassurances custom resource:

  ```bash
  kubectl edit imageassurances default
  ```

- Set the ```clusterScanner``` to ```Enabled``` and save

  ```yaml
  apiVersion: image-assurance.operator.tigera.io/v1
  kind: ImageAssurance
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"image-assurance.operator.tigera.io/v1","kind":"ImageAssurance","metadata":{"annotations":{},"name":"default"},"spec":null}
    creationTimestamp: "2023-06-28T20:12:32Z"
    generation: 3
    name: default
    resourceVersion: "169600"
    uid: be896418-7afd-4e99-b366-cc49105946aa
  spec:
    clusterScanner: Enabled
    containerdVolumeMountPath: /var/lib/containerd/
    criSocketPath: /run/containerd/containerd.sock
  ```

- This will deploy the cluster scanner as a daemonset in your cluster. The scanner is deployed as a container inside ```tigera-image-assurance-crawdad``` daemonset. You can verify that the scanner is deployed by verifying that a new container with name ```cluster-scanner``` is created inside the daemonset.

  ```bash
  kubectl get pods -n tigera-image-assurance -oyaml | grep cluster-scanner
  ```

  ```bash
  image: quay.io/tigera/image-assurance-cluster-scanner:v1.7.3
  name: cluster-scanner
  image: quay.io/tigera/image-assurance-cluster-scanner:v1.7.3
  imageID: quay.io/tigera/image-assurance-cluster-scanner@sha256:fd80425f7b1ebbf7b7124f2fe6fe319ddd3f471c86ccc8299233494db6bf1dd0
  name: cluster-scanner
  hash.operator.tigera.io/cluster-scanner-image-assurance-api-token: 5bd5d673f525a796e5854602a18c74a596b531d0
        name: tigera-image-assurance-cluster-scanner-api-access
  image: quay.io/tigera/image-assurance-cluster-scanner:v1.7.3
  name: cluster-scanner
  image: quay.io/tigera/image-assurance-cluster-scanner:v1.7.3
  imageID: quay.io/tigera/image-assurance-cluster-scanner@sha256:fd80425f7b1ebbf7b7124f2fe6fe319ddd3f471c86ccc8299233494db6bf1dd0
  name: cluster-scanner
  ```

- Now you can check in the Image Assurance UI for all the images in ithe cluster scanned by the in-cluster scanner that show up that weren't actually scanned by the CLI scanner before manually.

[:arrow_right: Module 7 - Runtime security with IDS/IPS using Deep Packet Inspection](module-7-runtimesec.md) <br>

[:arrow_left: Module 5 - Calico Cloud Admission Controller](module-5-admission-controller.md)

[:leftwards_arrow_with_hook: Back to Main](../README.md)
