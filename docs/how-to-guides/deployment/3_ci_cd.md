# How to configure deployment CI/CD

In parts [1](./1_containers.md) and [2](./2_kubernetes.md) we have created a container and corresponding kubernetes manifests which describe the deployment of that container.
This guide now focuses on how those manifests can be deployed and updated automatically.
To do this we will use a mixture of *GitHub Actions* for Continuous Integration and *ArgoCD* for Continuous Deployment.
We will also set up our three stages *prod*, *stage* and *dev*.

!!! important "Goal"

    After following this guide, you will have automatically deployed an example application in three stages *prod*, *stage* and *dev*.

!!! important "Prerequisites"

    This guide assumes that you already have a repository with a working Dockerfile as well as generic manifests that *just* need to be deployed.


## 1) Build Container automatically

First we need to set up CI for our application repository so that its docker image is built automatically.
To do this, create the file `.github/workflows/container_image.yml` and fill it with the following content.

!!! info

    For more details about the workflow file see the [Github Action Syntax Reference](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions) as well as our repositoriy of [reusable workflows](https://github.com/Viva-con-Agua/workflows/).

```yaml title=".github/workflows/contianer_image.yml"
name: container_image
on:
  push:
    branches:
      - main
      - stage
      - develop

jobs:
  build-container:
    uses: Viva-con-Agua/workflows/.github/workflows/build_image.yml@main
  deploy:
    needs: [build-container]
    uses: Viva-con-Agua/workflows/.github/workflows/deploy.yml@main
    secrets: inherit
    with:
      image_name: ${{ needs.build-container.outputs.image_name }}
      new_digest: ${{ needs.build-container.outputs.image_digest }}
```

What this workflow achieves is:

- run the workflow whenever a change is pushed to one of the branches *main*, *stage* or *develop*
- build a container image and automatically tag it based on the branch
- deploy the just-built image


## 2) Create *-deploy* repository

Step 1) just *deploys* the image however what this actually means is that it updates the digest[^1] which should be deployed in another repository.
We call this other repository the *-deploy* repository.
So, in order for GitHub Actions to update the digest the *-deploy* repository needs to actually exist.

[^1]: Digests are a content-addressable identifier of an image. As long as the input used to generate the image is unchanged, the digest value is predictable.

!!! important

    The *-deploy* repository should be named like your app repository in addition to a *-deploy* postfix.

### 2.1) Create *dev* stage folder

Each *-deploy* repository is structured to contain one folder for each deployment stage.
We start with the *dev* stage which means you simply need to create a folder called `dev`.

```shell title="Create dev folder"
mkdir dev
```

### 2.2) Create additional manifests

Remember that in [part 2](./2_kubernetes.md) we created kubernetes manifests that are very general and don't include any deployment details.
This means that such deployment details need to be added now since the *-deploy* repositories different stage folders are exactly where such details are known.

=== "Ingress"

    An Ingress definition is very important because it defines how our app is reachable via http from the outside network.
    A simple manifest that terminates all traffic to `https://example-app.dev.vivaconagua.org` (and http with automatic redirects) and then forwards it to the service called `example-app` can be seen below.

    !!! info

        Relevant Kubernetes documentation is about [Ingresses](https://kubernetes.io/docs/concepts/services-networking/ingress/), backing [services](https://kubernetes.io/docs/concepts/services-networking/service/) and available [ingress manifest fields](https://kubernetes.io/docs/reference/kubernetes-api/service-resources/ingress-v1/).

    ```yaml title="dev/ingress.yml"
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: example-app
      annotations:
        cert-manager.io/cluster-issuer: letsencrypt-prod
    spec:
      tls:
        - hosts: [ example-app.dev.vivaconagua.org ]
          secretName: example-app.dev.vivaconagua.org-tls
      rules:
        - host: example-app.dev.vivaconagua.org
          http:
            paths:
              - path: /
                pathType: Prefix
                backend:
                  service:
                    name: example-app
                    port:
                      name: http
    ```

=== "Namespace"

    We also want to put all resources into their own namespace to keep things organized.
    To do so simply create the manifest file that can be seen below.
    
    !!! info
    
        See relevent Kubernetes [namespace documentation](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) and available [namespace manifest fields](https://kubernetes.io/docs/reference/kubernetes-api/cluster-resources/namespace-v1/).
    
    ```yaml
    apiVersion: v1
    kind: Namespace
    metadata:
        name: example-app--dev
    ```

### 2.4) Write *dev* kustomization

Now that a folder for the *dev* stage exists and contains all deployment relevant manifests we just need to bundle them together so that the whole stage can be deployed in unison.

```yaml title="dev/kustomization.yml"
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: example-app--dev
commonLabels:
  app.kubernetes.io/part-of: example-app
  app.kubernetes.io/instance: dev

resources:
- https://github.com/Viva-con-Agua/example-app.git?ref=develop
- ingress.yml
- namespace.yml

images: []
```


## 3) Register app with *ArgoCD*

The app is now theoretically completely deployable, but it still needs to actually be deployed.
This is done using *ArgoCD*[^2].
All our applications are defined inside the repository [github.com/Viva-con-Agua/argocd-apps](https://github.com/Viva-con-Agua/argocd-apps/).

[^2]: See our [ArgoCD reference](../../references/devops/cd.md) about how we have set it up and how it works.

Add a manifest for your application definition like the following.
This manifest will instruct *ArgoCD* to scan for folders inside the *-deploy* repository and creates an appropriate application definition for every folder it finds.
Since our *-deploy* repositories contain one folder for each deployed stage, *ArgoCD* creates a separate application for each stage.

!!! info

    The relevant documentation is from *ArgoCD* about [Applications](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#applications) and [ApplicationSets](https://argo-cd.readthedocs.io/en/stable/user-guide/application-set/).

```yaml title="vca-apps/example-app.yml"
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: example-app
spec:
  generators:
    - git:
        repoURL: https://github.com/Viva-con-Agua/example-app-deploy.git
        revision: HEAD
        directories:
          - path: "*"
  template:
    metadata:
      name: "example-app--{{path}}"
    spec:
      destination:
        name: in-cluster
      project: "vca-{{path}}"
      source:
        repoURL: https://github.com/Viva-con-Agua/example-app-deploy.git
        targetRevision: HEAD
        path: "{{path}}"
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

Additionally don't forget to also include this app definition in the repositories `kustomization.yml`

```yaml title="kustomization.yml" hl_lines="9"
apiVersion: kustomize.config.k8s.io/v1beta1
namespace: argocd
resources:
  - basic-cluster/project.yml
  - basic-cluster/argocd-apps.yml
  - …

  - vca-apps/projects.yml
  - vca-apps/example-app.yml
  - …
```


## 4) Monitor status on *ArgoCD*

As said above, the actual deployment is now done by *ArgoCD,* but you can monitor it over at [https://argocd.vivaconagua.org](https://argocd.vivaconagua.org).
