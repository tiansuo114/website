---
title: Deploying Karmada Using Configuration File with CLI
---

This documentation serves as a comprehensive guide for deploying Karmada using a configuration file through the `karmadactl` CLI tool.

## Prerequisites

- A Kubernetes cluster ready for Karmada installation.

- A valid kube-config file to access your Kubernetes cluster.

- The karmadactl command-line tool or kubectl-karmada plugin installed.

  :::note

  For installing `karmadactl` command line tool refer to the [Installation of CLI Tools](/docs/next/installation/install-cli-tools#one-click-installation) guide.

  :::

## Installation

`karmadactl init` allows you to install Karmada by specifying a configuration file, which provides a structured way to define all settings in a YAML file.

### Example Configuration File
Here is an example of the configuration file for Karmada deployment:
```yaml
apiVersion: config.karmada.io/v1alpha1
kind: KarmadaInitConfig
metadata:
  name: karmada-init
spec:
  karmadaCrds: "https://github.com/karmada-io/karmada/releases/download/v1.10.3/crds.tar.gz"
  etcd:
    local:
      imageRepository: "registry.k8s.io/etcd"
      imageTag: "3.5.13-0"
      initImage:
        imageRepository: "docker.io/library/alpine"
        imageTag: "3.19.1"
  components:
    karmadaAPIServer:
      imageRepository: "registry.k8s.io/kube-apiserver"
      imageTag: "v1.30.0"
    karmadaAggregatedAPIServer:
      imageRepository: "docker.io/karmada/karmada-aggregated-apiserver"
      imageTag: "v1.10.3"
    kubeControllerManager:
      imageRepository: "registry.k8s.io/kube-controller-manager"
      imageTag: "v1.30.0"
    karmadaControllerManager:
      imageRepository: "docker.io/karmada/karmada-controller-manager"
      imageTag: "v1.10.3"
    karmadaScheduler:
      imageRepository: "docker.io/karmada/karmada-scheduler"
      imageTag: "v1.10.3"
    karmadaWebhook:
      imageRepository: "docker.io/karmada/karmada-webhook"
      imageTag: "v1.10.3"
```

### Deploying Karmada with Configuration File

1.Save the example configuration above to a file, e.g., karmada-config.yaml.

2.Use the following command to deploy Karmada with the configuration file:

  ```bash
  sudo karmadactl init --karmada-apiserver-replicas 3 --etcd-replicas 3 --etcd-storage-mode PVC --storage-classes-name <storage-classes-name> --kubeconfig <path-to-kube-config>
  ```

  :::note
    
  You need to use sudo for elevated permissions because `karmadactl` creates a
  `karmada-config` file in the `/etc/karmada/karmada-apiserver.config` directory.


  :::

3.This configuration file allows you to define essential parameters, including:

- certificates: Defines certificate paths and validity period.
- etcd: Configures Etcd settings, including local or external Etcd options.
- hostCluster: Specifies host cluster API endpoint and kubeconfig.
- images: Configures image settings for components.
- components: Sets up replicas for API Server and other core components.
- karmadaDataPath: Defines the data path for Karmada.
-  ...

If you need more information about the configuration file, please refer to the [karmadactl init API reference](/docs/reference/karmadactl/karmadactl-config.v1alpha1.md).

## Verification

Once the Karmada cluster installation is complete, you can verify the distribution of
pods across multiple nodes by using the following command:

```bash
kubectl get pod -o=custom-columns=NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName -n karmada-system
```

This will display the status and node allocation of pods within the `karmada-system`
namespace.