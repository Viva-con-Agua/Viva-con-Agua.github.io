# Secret Management

Because we implement a GitOps style deployment and secrets should not be stored in plaintext in a git repository we require a form of secret management.
To solve this, we use the [pass](https://www.passwordstore.org/) password manager to store and manage all our secrets (not just passwords) encrypted in a private repository[^1].

[^1]: [https://github.com/Viva-con-Agua/pass/](https://github.com/Viva-con-Agua/pass/)

## Kustomize Integration

We have decided to retrieve secrets from *pass* during the Kubernetes manifest generation.
Since our Kubernetes are generated with kustomize this means that kustomize needs to extract secrets during that step.
To accomplish this we use [kustomize-pass](https://github.com/ftsell/kustomize-pass/) to transform references to secrets into their actual secret content.

??? example

    The following reference to a secret would be automatically looked up during the manifest generation and transformed into the kubernetes secret shown afterwards (assuming the referenced secret actually contains the example data):

    ```yaml
    # reference to secret
    apiVersion: ftsell.de/v1beta1
    kind: PassSecret
    metadata:
      name: example-secret
      annotations:
        config.kubernetes.io/function: |
          exec:
            path: kustomize-pass
    data:
      example-key: example-pass-name
    ```

    ```yaml
    # generated kubernetes secret
    apiVersion: v1
    kind: Secret
    metadata:
      name: example-secret
    data:
      example-key: foobar123
    ```


## ArgoCD Integration

*ArgoCD* performs our final deployment[^2].
Naturally it needs to be able to resolve the references to pass secrets.
To do this, a few patches to the default *ArgoCD* deployment have been applied:

- The deployed container image has been changed to include the *kustomize-pass* binary
- A secret gpg key for *ArgoCD* has been added to our pass password store and been provided to the running *ArgoCD*.

    The key has the id `BC5FD89696FE550BE835D67478BFB0094BE70CBB` and is stored in pass itself under `services/basic-cluster/argocd/gpg.key`.

With these patches, *ArgoCD* can call *kustomize* normally which in turn is able to resolve the secrets as described above.

[^2]: See [Continuous Delivery documentation](./devops/cd.md)
