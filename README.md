# OpenTelemetry Manifests

[![Overall](https://img.shields.io/endpoint?style=flat&url=https%3A%2F%2Fapp.opslevel.com%2Fapi%2Fservice_level%2Fmag0hvpki88PEiM5mCYyWTo49M6sOFLitqAZ-cJw9y0)](https://app.opslevel.com/services/otel-collector/maturity-report)

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
