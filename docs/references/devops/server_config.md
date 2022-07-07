# Server Config & Orchestration

Currently, our servers are configured manually and without any form of central configuration management.

Nonetheless, for the future we have an [ansible](https://www.ansible.com/) repository
at [viva-con-agua/ansible](https://github.com/Viva-con-Agua/ansible) which we sometimes use for new configuration.
How the ansible repository can be used to author and deploy changes is documented in the repository itself.

## Kubernetes Configuration

The configuration and deployment of Kubernetes itself is done via [kubespray](https://kubespray.io/#/) which is
configured
from the aforementioned [ansible repository](https://github.com/Viva-con-Agua/ansible/).
See that repository for exact details but generally, we use one control plane node and two
workers, [calico](https://projectcalico.docs.tigera.io/about/about-calico) for networking and [cri-o](https://cri-o.io/)
as container runtime.

## Installed Cluster Apps

In addition to the bare kubernetes cluster we also have some applications deployed on it which make development and
operations a lot easier.
The following applications are currently deployed:

| App                                                                         | Description                                                                                                                                                                                                                                      |     | Access Link                             | deploy-repo                                                                                             |
|-----------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|-----------------------------------------|---------------------------------------------------------------------------------------------------------|
| [Ingress Nginx](https://kubernetes.github.io/ingress-nginx/)                | Kubernetes [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) implementation (basically a reverse proxy server for other in-cluster services)                                                                           |     | *n/a*                                   | [k8s-ingress-deploy](https://github.com/Viva-con-Agua/k8s-ingress-deploy)                               |
| [cert-manager](https://cert-manager.io/)                                    | Automated TLS Certificate management (think certbot but kubernetes)                                                                                                                                                                              |     | *n/a*                                   | [cert-manager-deploy](https://github.com/Viva-con-Agua/cert-manager-deploy)                             |
| [Local Path Provisioner](https://github.com/rancher/local-path-provisioner) | [Dynamic provisioning](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) of persistent local node storage to kubernetes workloads via [PersistentVolumeClaims](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) |     | *n/a*                                   | [k8s-local-path-provisioner-deploy](https://github.com/Viva-con-Agua/k8s-local-path-provisioner-deploy) |
| [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)                         | Continuous Delivery engine which allows us to do git-ops style deployment of our other apps. See [specific documentation page](./cd.md).                                                                                                         |     | [click](https://argocd.vivaconagua.org) | [argocd-deploy](https://github.com/Viva-con-Agua/argocd-deploy)                                         |

