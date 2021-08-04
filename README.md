# Deploy Prometheus in K8s

This deploys a Prometheus stack in Kubernetes. I'm running this
locally in [k3s](https://k3s.io/), so there may be some quirks that
are unnecessary in e.g. an OpenShift environment (specifically around
PV ownership).

The full stack includes:

- [PromScale](https://github.com/timescale/promscale)
  - [TimeScaleDB](https://www.timescale.com/)

- [Thanos](https://thanos.io/)

- [Grafana](https://grafana.com/)

- [SNMP Exporter](https://github.com/prometheus/snmp_exporter)

Everything is deployed using [Kustomize](https://kustomize.io). I'm
  using [Bitnami Sealed
  Secrets](https://github.com/bitnami-labs/sealed-secrets) for
  secrets.

## Deploying

I deploy this stack by running:

```
kustomize build | kubectl apply -f-
```

The top level `kustomization.yaml` includes a few common resources
directly, but for the most part operates by including subdirectories
each of which has its own `kustomization.yaml` file:

```
resources:
  - namespaces/prometheus.yaml
  - sealedsecrets/thanos-bucket-config.sealed.yaml

  - timescaledb
  - promscale
  - prometheus
  - grafana
  - snmp-exporter
  - redirector
  - thanos
```
