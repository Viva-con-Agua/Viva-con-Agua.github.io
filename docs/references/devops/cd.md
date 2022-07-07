# Continuous Delivery

Using continuous delivery, we aim to deploy our software as soon as it is tested and verified in an automated process.

!!! cite "[What is GitOps? | GitLab](https://about.gitlab.com/topics/gitops/)"

    There are four key components to a GitOps workflow, a Git repository, a continuous delivery (CD) pipeline, an application deployment tool, and a monitoring system.

    1. **The Git repository is the source of truth for the application configuration and code.**
    2. The CD pipeline is responsible for building, testing, and deploying the application.
    3. **The deployment tool is used to manage the application resources in the target environment.**
    4. The monitoring system tracks the application performance and provides feedback to the development team.

In this document, we focus on points 1. and 3., the *git repository* and *deployment tool*.
For us, the latter is [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) (access [here](https://argocd.vivaconagua.org/)).

## How ArgoCD works

![Event Diagram of how argo works](../../assets/argocd-how-it-works.drawio.svg)

During any deployment process, a few steps are performed:

1. A user changes something in a deployment repository that *ArgoCD* is configured to monitor.
2. The repository provider (GitHub but others are possible too) sends a webhook to *ArgoCD* to notify it that something
    changed in this repository.
    The content of the event is mostly ignored by *ArgoCD* except that it triggers a refresh.
3. *ArgoCD* performs a refresh of the repositories content.

    !!! note
        This step still works if no webhook is configured because *ArgoCD* regularly performs this refresh anyways.
        Nonetheless, an organization level webhook is configured for all our GitHub repositories.

    This refresh includes building the manifests contained in that repository which also includes fetching secrets from pass[^1].

4. Based on the completely built manifests *ArgoCD* synchronizes the Kubernetes cluster to be as the manifests describe.
5. One the application is deployed or updated, the user can access and use it.

[^1]: See the [Secret Management](../secret_management.md) documentation for details on how *ArgoCD* fetches secrets from pass.
