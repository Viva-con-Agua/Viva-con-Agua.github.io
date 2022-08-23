# How to auto-restart failed apps

!!! important "Goal"

    This shows how probes can be added to Kubernetes deployments so that the underlying application is automatically restarted or excluded from receiving requests.

!!! important "Prerequisites"

    This guide assumes that you already have an application and a deployment manifest for it.

!!! info

    Also see the upstream Kubernetes [documentation about probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

Generally speaking Kubernetes supports three kinds different kinds of probes.
All of these are run by Kubernetes at regular (configurable) intervals to check the health and readiness of a container.


## Restart unhealthy apps

The first probe we'll consider is called a *liveness probe*.
Its job is to probe the container to check if it is still alive and restarts it when it is not.

Adding a liveness probe is simple enough.
In the following example an HTTP probe is added to check the `/healthz` route.
Any status code greater than or equal to 200 and less than 400 indicates success. 
Any other code indicates failure.

```yaml hl_lines="11 12 13 14"
apiVersion: v1
kind: Pod
metadata:
  name: example-liveness-http
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/liveness
    args:
    - /server
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
```

!!! info "What makes a liveness check"

    Liveness checks should only check the bare minimum of a running container.
    They should never include checks of required dependent services.
    They should also be configured with more generous failure thresholds than readiness probes.


## Starve unready apps

Another useful probe is a *readiness probe*.
Similar to liveness probes, Kubernetes regularly runs it to check whether the container is ready to accept traffic.
If not, the container is removed from any upstream services and no new requests are delivered to it.

In the following example an HTTP probe is configure to check the `/readiness` route.
Any status code greater than or equal to 200 and less than 400 indicates success. 
Any other code indicates failure.

```yaml hl_lines="11 12 13 14"
apiVersion: v1
kind: Pod
metadata:
  name: example-liveness-http
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/liveness
    args:
    - /server
    readinessProbe:
      httpGet:
        path: /healthz
        port: 8080
```

!!! info "What makes a readiness check"

    Readiness checks should check whether the app could serve a request right now.
    It should however still not check shared dependencies[^1][^2].
    Instead it should check internal state (e.g. request queue is not too long) and it *may* check availability of exclusive dependencies.

[^1]: [Kubernetes Liveness and Readiness Probes: How to Avoid Shooting Yourself in the Foot](https://blog.colinbreck.com/kubernetes-liveness-and-readiness-probes-how-to-avoid-shooting-yourself-in-the-foot/)
[^2]: [Liveness Probes are Dangerous](https://srcco.de/posts/kubernetes-liveness-probes-are-dangerous.html)
