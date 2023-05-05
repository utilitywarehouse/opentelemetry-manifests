# OpenTelemetry Manifests

Namespace that runs [OpenTelemetry](opentelemetry.io/) Collector to inject,
process & export traces for teams at UW.

![Design](./otel.svg)

## Upgrade

### Collector

  - Tag a new release in this repo
  - Update each otel namespace [in the kubernetes manifests
    repo](https://github.com/utilitywarehouse/kubernetes-manifests) that uses
    `github.com/utilitywarehouse/opentelemetry-manifests//base?ref={version}`
  - Update places not using the base, but using the image
    `otel/opentelemetry-collector-contrib` directly
