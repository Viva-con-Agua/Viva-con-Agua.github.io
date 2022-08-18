# How to create Kubernetes manifests

In [part 1](./1_containers.md) of this series we have created a container image that includes a Vue application.
This part now describes how that container can be deployed in a Kubernetes environment.
For a detailed description of what kubernetes does and why we use it see the relevant explanation [here](../../explanations/kubernetes.md).

Similar to how containers should not be fine-tuned to our infrastructure and could be reused by others, the manifests that are created in this guide are also intended to be general.

!!! important "Goal"

    After following this guide, you will have a series of Kubernetes manifests that describe how the application is deployed.

!!! important "Prerequisites"

    This guide assumes that you already have a working container image for your application.
    Additionally your app should only respond to HTTP traffic without requiring any backing services like databases or persistent storage.


## 1) Create a deployment

First we create a deployment which manages the lifecycle of our container.
To do that create the file `.k8s/deployment.yml` in your app folder.

Reading the Kubernetes documentation about [describing objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) and about [deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) as well as referencing the list of available [deployment manifest fields](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/deployment-v1/) will help you understand how the manifest file is structured and what the used fields do.

!!! info

    We generally prefer to keep files that are not directly part of the application source code and which are only
    required as part of kubernetes manifests inside the `.k8s` folder.

```yaml title=".k8s/deployment.yml"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-app
  labels: &labels
    app.kubernetes.io/name: example-app
    app.kubernetes.io/component: frontend
spec:
  selector:
    matchLabels: *labels
  template:
    metadata:
      labels: *labels
    spec:
      containers:
        - name: example-app
          image: ghcr.io/viva-con-agua/example-app
          imagePullPolicy: Always
          ports:
            - name: http
              containerPort: 80
```


## 2) Create a service

In addition to the deployment our app also needs a service to route traffic to the created containers.

The relevant Kubernetes documentation is the one about [services](https://kubernetes.io/docs/concepts/services-networking/service/) and the list of available [service manifest fields](https://kubernetes.io/docs/reference/kubernetes-api/service-resources/service-v1/).

```yaml title=".k8s/service.yml"
apiVersion: v1
kind: Service
metadata:
  name: example-app
  labels: &labels
    app.kubernetes.io/name: example-app
    app.kubernetes.io/component: frontend
spec:
  selector: *labels
  ports:
    - name: http
      port: 80
      targetPort: http
```


## 3) Bundle manifests together

At this point all strictly required manifests exist, but we still want to bundle them together so that they can be applied *as one*.
To do that, we create a `kustomization.yml` file.
It is the configuration file of [kustomize](https://kustomize.io/), and you should see the [kustomization reference](https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/) about the used fields.

```yaml title="kustomization.yml"
apiVersion: kustomize.config.k8s.io/v1beta1
commonLabels:
  app.kubernetes.io/part-of: example-app

resources:
  - .k8s/deployment.yml
  - .k8s/service.yml
```

What this file achieves is the following:

- Bundle the manifests together
- Add the label defined via `commonLabels` to all resources as well as all relevant resource selectors
