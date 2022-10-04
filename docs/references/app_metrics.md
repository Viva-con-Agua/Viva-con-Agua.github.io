# Application Metrics Collection

Our observability stack based on [prometheus](https://prometheus.io/) is configured to automatically discover and scrape application metrics from apps deployed on kubernetes.


## Viewing Metrics

Just visit [https://observability.vivaconagua.org/,](https://observability.vivaconagua.org/) and you will see our [Grafana](https://grafana.com) dashboard.
There you can view one of the preexisting dashboards as well as explore all collected metrics.


## Exposing Metrics

To expose metrics, read the official prometheus documentation about [instrumenting an app](https://prometheus.io/docs/instrumenting/clientlibs/).

Finally, in order for metrics to be collected, the kubernetes manifests of that app need to be annotated correctly.
You can annotate pods as well as services:

- If you annotate pods inside a deployment template, all pods of that deployment will get scraped.
    This might be useful for metrics that show e.g. how much memory an app process uses.
- If you annotate services, kubernetes will route the metrics collection request of prometheus to one random backing pod.
    This type of collection might be useful to collect aggregated application metrics e.g. number of registered users.

!!! info "Kubernetes Annotations"

    | Annotation | Default | Description |
    |--- | --- | --- |
    | `prometheus.io/scrape` | `false` | Must be set to `true` to enable scraping at all  |
    | `prometheus.io/metric_path` | `/metrics` | HTTP path under which metrics are served |
    | `prometheus.io/port` | *the first port defined on the pod or service* | Which port prometheus scrapes for metrics |
    | `prometheus.io/scheme` | `http` | HTTP scheme which prometheus uses to scrape for metrics |
