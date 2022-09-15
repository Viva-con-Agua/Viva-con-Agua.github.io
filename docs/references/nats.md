# NATS

We have deployed [NATS](https://nats.io) on our infrastructure as a message broker to communicate between our different services.

Its deployment configuration is located in the [nats-deploy](https://github.com/Viva-con-Agua/nats-deploy) repository on GitHub and details about the deployment are described there.

## Connecting to NATS

*NATS* is exposed to the rest of the cluster via a service object simply called `nats` inside its namespace.
This means that connections are possible via the hostname associated to that service, e.g. `nats.nats-dev.svc.cluster.local`.

!!! important "NATS Hostname"

    **`nats.nats-dev.svc.cluster.local`** but `dev` can also be `stage` or `prod` to use that NATS instance instead.

### Interactive Session from inside the Cluster

*kubectl* supports running one-off pods in the cluster.
We use this capability to run a [nats-box](https://github.com/nats-io/nats-box) pod and use the installed tools there.
```shell
kubectl run -it --rm nats-box --image=natsio/nats-box
```

You can now verify the server connection by running:
```shell
nats -s nats.nats-dev.svc.cluster.local server check
```

### Port-Forwarding to local computer

*kubectl* supports port-forwarding arbitrary services to your local computer.

The following command port-forwards the port named *nats-client* on the service named *nats* inside the namespace *nats-dev*.
```shell
kubectl -n nats-dev port-forward svc/nats nats-client
```

You can now verify the server connection by running:
```shell
nats -s localhost server check
```
