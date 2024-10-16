# OpenTelemetry Manifests

[![Overall](https://img.shields.io/endpoint?style=flat&url=https%3A%2F%2Fapp.opslevel.com%2Fapi%2Fservice_level%2Fmag0hvpki88PEiM5mCYyWTo49M6sOFLitqAZ-cJw9y0)](https://app.opslevel.com/services/otel-collector/maturity-report)

Namespace that runs [OpenTelemetry](https://opentelemetry.io/) (OTEL) Collector
to inject, process & export traces for teams at UW.

Repository also contains manifest for [Tempo](https://grafana.com/docs/tempo/latest/) backend,
allowing querying and browsing traces in Grafana UI.


``` mermaid
flowchart LR
    Pod[
        **pod**
        Running our applications with OpenTelmetry instrumentation
    ] -- "quickly offload traces" --> Collector
    PubClients[
        **Public Clients**
        Public clients, like web or mobile applocations,
        running our applications with OpenTelmetry instrumentation
    ] --> CollectorWeb

    subgraph OTEL[OpenTelemetry stack]
    direction LR
        Collector[
            **collector**
            additional handling like retries, batching
        ]
        CollectorWeb["
            **web collector (optional)**
            second collector, meant specificially for traffic from public apps
        "]
        CollectorWeb-->Collector
    end OTEL

    Collector -- "via Collector's Exporter" --> Kafka
    Kafka -- "via Tempo's Distributor" --> Grafana

    subgraph Grafana[Grafana stack]
    Tempo["
        **tempo**
        provides storage and querying of traces
    "]
    end Grafana
```

For the architecture of the 3rd party pieces see:

  - [OpenTelemetry
    Collector](https://opentelemetry.io/docs/collector/architecture/)
  - [Tempo](https://grafana.com/docs/tempo/latest/operations/architecture/)


We only run Tempo in one of our clusters, so across clusters the structure is:

``` mermaid
flowchart LR
    subgraph aws[AWS]
    direction LR
        CollectorAWS[Collector]
        CollectorAWSWeb[Collector Web]
        KafkaAWS[Kafka]
        Tempo

        PodAWS[Pod] ---> CollectorAWS
        CollectorAWS --> KafkaAWS
        CollectorAWSWeb --> CollectorAWS
        KafkaAWS --> Tempo
    end aws

    subgraph merit[merit]
    direction LR
        PodMerit[Pod] ---> CollectorMerit[Collector]
        CollectorMerit --> KafkaAWS
    end merit

    subgraph gcp[GCP]
    direction LR
        PodGCP[Pod] ---> CollectorGCP[Collector]
        CollectorGCP --> KafkaAWS
    end gcp


    PublicClients[Public Clients] --> CollectorAWSWeb
```

## Upgrade

### Collector

  - Tag a new release in this repo
  - Update each otel namespace [in the kubernetes manifests
    repo](https://github.com/utilitywarehouse/kubernetes-manifests) that uses
    `github.com/utilitywarehouse/opentelemetry-manifests//base?ref={version}`
  - Update places not using the base, but using the image
    `otel/opentelemetry-collector-contrib` directly
