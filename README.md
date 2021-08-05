# Deploy Prometheus in K8s

This deploys a Prometheus stack in Kubernetes. I'm running this
locally in [k3s](https://k3s.io/), so there may be some quirks that
are unnecessary in e.g. an OpenShift environment (specifically around
PV ownership).

The full stack includes:

- [Prometheus](https://prometheus.io/)

- [Thanos](https://thanos.io/)

  I use thanos to ship metrics to an S3 bucket on a remote fileserver
  both (a) for long term storage and (b) to preserve history in the
  event that I blow away the local installation at some point.

- [Grafana](https://grafana.com/)

- [SNMP Exporter](https://github.com/prometheus/snmp_exporter)

  I use the SNMP exporter to collect data from my home router.

Everything is deployed using [Kustomize](https://kustomize.io). I'm
using [Bitnami Sealed
Secrets](https://github.com/bitnami-labs/sealed-secrets) for secrets.

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

  - prometheus
  - grafana
  - snmp-exporter
  - redirector
  - thanos
```
