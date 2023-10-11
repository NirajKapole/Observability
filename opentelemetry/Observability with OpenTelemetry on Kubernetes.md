**Observability with OpenTelemetry on Kubernetes**

Observability is the ability to gain insight into the behavior and performance of a system through its telemetry data, including metrics, logs, and traces. OpenTelemetry is a vendor-neutral, open-source framework that provides a standardized way to instrument, collect, and export telemetry data from distributed systems.

This document will guide you through the process of setting up observability using OpenTelemetry, including installing cert-manager, Helm, OpenTelemetry, and Tempo, as well as creating YAML files and deploying applications.

**Index Topics**

|Index|Content|
| :- | :- |
|1|Cert-manager using kubectl approach - What it is and how to install it on a Kubernetes cluster|
|2|Helm - What it is, Helm-charts, and how to install it on a Kubernetes cluster|
|3|OpenTelemetry - What it is and how to install it on a Kubernetes cluster using Helm charts|
|4|OpenTelemetry Collector and Sidecar mode - How to create YAML files for both|
|5|Node app and Java app deployment using OpenTelemetry|
|6|Tempo - What it is and how to install it using a Helm chart|

**What is cert-manager? and Installation on kubernetes cluster.**

Cert-manager is a Kubernetes add-on that automates the management and issuance of TLS certificates from various certificate authorities (CAs). It provides an easy way to secure your Kubernetes applications by automatically obtaining and renewing TLS certificates.

You can install cert-manager via [cert-manager](https://cert-manager.io/docs/installation/kubectl/). More information is available [here](https://cert-manager.io/docs/installation/kubectl/).

**What is Helm? What are Helm-charts and Installation on kubernetes cluster**.

Helm is a package manager for Kubernetes that allows you to easily install, manage, and upgrade applications on your cluster. Helm-charts are preconfigured packages that contain all the resources needed to deploy an application on Kubernetes.

To install Helm on your Kubernetes cluster, follow these steps:

Download the Helm binary from the official Helm website:

$ curl -fsSL -o get\_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

$ chmod 700 get\_helm.sh

$ ./get\_helm.sh

Update the Helm repository:

helm repo update

**What is OpenTelemetry and its components& Installation on k8s cluster using Helm-charts.**

OpenTelemetry is a framework that allows you to instrument your applications to collect telemetry data such as traces, metrics, and logs. It provides a vendor-neutral, open-source way to collect telemetry data from distributed systems.

**Components:** 

The Collector consists of three components that access telemetry data:

**Receiver**

A receiver, which can be push or pull based, is how data gets into the Collector. Receivers may support one or more [data sources](https://opentelemetry.io/docs/concepts/signals/).

The receivers: section is how receivers are configured. Many receivers come with default settings so simply specifying the name of the receiver is enough to configure it (for example, otlp:). If configuration is required or a user wants to change the default configuration then such configuration must be defined in this section. Configuration parameters specified for which the receiver provides a default configuration are overridden.

**Processors**

Processors are run on data between being received and being exported. Processors are optional though [some are recommended](https://github.com/open-telemetry/opentelemetry-collector/tree/main/processor#recommended-processors).

The processors: section is how processors are configured. Processors may come with default settings, but many require configuration. Any configuration for a processor must be done in this section. Configuration parameters specified for which the processor provides a default configuration are overridden.

**Exporters**

An exporter, which can be push or pull based, is how you send data to one or more backends/destinations. Exporters may support one or more [data sources](https://opentelemetry.io/docs/concepts/signals/).

The exporters: section is how exporters are configured. Exporters may come with default settings, but many require configuration to specify at least the destination and security settings. Any configuration for an exporter must be done in this section. Configuration parameters specified for which the exporter provides a default configuration are overridden.

**Services**

The service section is used to configure what components are enabled in the Collector based on the configuration found in the receivers, processors, exporters, and extensions sections. If a component is configured, but not defined within the service section then it is not enabled. The service section consists of three sub-sections: 

**Pipelines**

Pipelines can be of the following types:

traces: collects and processes trace data.

logs: collects and processes log data.

A pipeline consists of a set of receivers, processors and exporters. Each receiver/processor/exporter must be defined in the configuration outside of the service section to be included in a pipeline.

Note: Each receiver/processor/exporter can be used in more than one pipeline. For processor(s) referenced in multiple pipelines, each pipeline will get a separate instance of that processor(s). This is in contrast to receiver(s)/exporter(s) referenced in multiple pipelines, where only one instance of a receiver/exporter is used for all pipelines. Also note that the order of processors dictates the order in which data is processed.

**Telemetry**

Telemetry is where the telemetry for the collector itself can be configured. It has two subsections: logs and metrics

The logs subsection allows configuration of the logs generated by the collector. By default the collector will write its logs to stderr with a log level of INFO. You can also add static key-value pairs to all logs using the initial fields section .[View the full list of metrics options here](https://github.com/open-telemetry/opentelemetry-collector/blob/7666eb04c30e5cfd750db9969fe507562598f0ae/config/service.go#L41-L97)

[Traces](https://opentelemetry.io/docs/concepts/observability-primer/#distributed-traces) give us the big picture of what happens when a request is made to an application. Whether your application is a monolith with a single database or a sophisticated mesh of services, traces are essential to understanding the full “path” a request takes in your application.

To install OpenTelemetry on your Kubernetes cluster using Helm charts, follow these steps:

Installation

Installation of Opentelemetry operator

Prequisites:

•	Kubernetes 1.19+ is required for OpenTelemetry Operator installation

•	Helm 3.9+

•	Cert-manager

Note: In Kubernetes, in order for the API server to communicate with the webhook component, the webhook requires a TLS certificate that the API server is configured to trust. There are three ways for you to generate the required TLS certificate.

The easiest and default method is to install the  and set admissionWebhooks.certManager.create to true. In this way, cert-manager will generate a self-signed certificate. See cert-manager installation for more details.

You can provide your own Issuer by configuring the admissionWebhooks.certManager.issuerRef value. You will need to specify the kind (Issuer or ClusterIssuer) and the name. Note that this method also requires the installation of cert-manager.

You can use self-signed certificate by configuring the admissionWebhooks.certManager.enabled to false. Helm will create self-signd cert and secret for you.

You can sideload custom webhooks and certificate by disabling .Values.admissionWebhooks.create and admissionWebhooks.certManager.enabled while setting your custom cert secret name in admissionWebhooks.secretName

You can disable webhooks alltogether by disabling .Values.admissionWebhooks.create and setting env var to ENABLE\_WEBHOOKS: "false"


Add Repository

$ helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts

$ helm repo update

Install Chart

$ helm install \opentelemetry-operator open-telemetry/opentelemetry-operator

Installation of Opentelemetry-collector

Prerequisites

Kubernetes 1.23+

Helm 3.9+

Installing the Chart

helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts

To install the chart with the release name my-opentelemetry-collector, run the following command:

helm install my-opentelemetry-collector open-telemetry/opentelemetry-collector

**Configuring Opentelemetry YAML file**  

apiVersion: opentelemetry.io/v1alpha1

kind: OpenTelemetryCollector

metadata:

`  `name: otel-collector

spec:

`  `mode: deployment

`  `hostNetwork: true

`  `config: |

`    `receivers:

`      `otlp:

`        `protocols:

`          `grpc:

`            `endpoint: "0.0.0.0:4317"

`          `http:

`            `endpoint: "0.0.0.0:4318"

`    `processors:

`    `exporters:

`      `logging:

`        `verbosity: detailed

`      `otlp:

`        `endpoint: http://my-tempo:4317

`        `tls:

`          `insecure: "true"

`      `jaeger:

`        `endpoint: http://my-tempo:14250

`        `tls: 

`          `insecure: "true"

`    `service:

`      `pipelines:

`        `traces:

`          `receivers: [otlp]

`          `processors: []

`          `exporters: [jaeger,otlp]

`        `logs:

`          `receivers: [otlp]

`          `exporters: [otlp]

`      `telemetry:

`        `logs:

`          `level: debug

**Configuring Opentelemetry-collector using sidecar mode.**

apiVersion: opentelemetry.io/v1alpha1

kind: OpenTelemetryCollector

metadata:

`  `name: otel

spec:

`  `mode: sidecar

`  `hostNetwork: true

`  `config: |

`    `receivers:

`      `otlp:

`        `protocols:

`          `grpc:

`          `http:

`    `processors:

`      `batch:

`      `memory\_limiter:

`        `limit\_mib: 400

`        `spike\_limit\_mib: 100

`        `check\_interval: 5s

`    `exporters:

`      `logging:

`        `verbosity: detailed

`      `otlp:

`        `endpoint: "otel-collector-collector.default:4317"

`        `tls:

`          `insecure: true

`        `sending\_queue:

`          `num\_consumers: 4

`          `queue\_size: 100

`        `retry\_on\_failure:

`          `enabled: true

`    `service:

`      `pipelines:

`        `traces:

`          `receivers: [otlp]

`          `processors: [memory\_limiter, batch]

`          `exporters: [otlp]

`        `metrics:

`          `receivers: [otlp]

`          `exporters: [otlp]

`        `logs:

`          `receivers: [otlp]

`          `exporters: [otlp]

`      `telemetry:

`        `logs:

`          `level: debug

**Configuring app.yaml file as a deployment using OpenTelemetry.**

apiVersion: apps/v1

kind: Deployment

metadata:

`  `name: node-app

spec:

`  `selector:

`    `matchLabels:

`      `app: node-app

`  `template:

`    `metadata:

`      `labels:

`        `app: node-app

`      `annotations:

`        `sidecar.opentelemetry.io/inject: "true"

`    `spec:

`      `terminationGracePeriodSeconds: 5

`      `containers:

`      `- name: server

`        `image: adityajoshi12/service-a:1.0

`        `imagePullPolicy: Always

`        `ports:

`        `- containerPort: 8080

`        `env:

`        `- name: OTEL\_EXPORTER\_OTLP\_ENDPOINT

`          `# value: "http://otel-collector.default.svc.cluster.local:4317"

`          `value: "http://otel-collector-collector:4317"

`        `- name: OTLP\_SERVICENAME

`          `value: "node-app"

`        `- name: SERVICE\_B

`          `value: "java-app:8080"

`        `resources:

`         `requests:

`            `memory: "64Mi"

`            `cpu: "250m"

`         `limits:

`            `memory: "128Mi"

`            `cpu: "500m"

\---

apiVersion: v1

kind: Service

metadata:

`  `name: node-app

spec:

`  `type: ClusterIP

`  `selector:

`    `app: node-app

`  `ports:

`  `- name: app

`    `port: 8080

\---

apiVersion: apps/v1

kind: Deployment

metadata:

`  `name: java-app

spec:

`  `selector:

`    `matchLabels:

`      `app: java-app

`  `template:

`    `metadata:

`      `labels:

`        `app: java-app

`      `annotations:

`        `sidecar.opentelemetry.io/inject: "true"

`    `spec:

`      `containers:

`        `- name: java-app

`          `image: adityajoshi12/service-b:1.0

`          `imagePullPolicy: Always

`          `ports:

`          `- containerPort: 8080

`          `env:

`          `- name: OTEL\_EXPORTER\_OTLP\_ENDPOINT

`            `value: "http://otel-collector-collector:4317"

`          `- name: OTEL\_TRACES\_EXPORTER

`            `value: "otlp"

`          `- name: OTEL\_RESOURCE\_ATTRIBUTES

`            `value: "service.name=java-app"

`          `resources:

`            `requests:

`                `memory: "128Mi"

`                `cpu: "250m"

`            `limits:

`                `memory: "248Mi"

`                `cpu: "500m"

\---

apiVersion: v1

kind: Service

metadata:

`  `name: java-app

spec:

`  `type: ClusterIP

`  `selector:

`    `app: java-app

`  `ports:

`  `- name: app

`    `port: 8080

**What is Tempo? and Installation using Helm-chart.**

Tempo is an open-source, easy-to-use, and scalable distributed tracing backend that supports the OpenTelemetry standard. It allows you to store and analyze your traces in a cost-effective way.

For more information refer via [source code](https://github.com/grafana/tempo).

To install Tempo on your Kubernetes cluster using Helm charts, follow these steps:

Add repository

helm repo add grafana https://grafana.github.io/helm-charts

Update repository

helm repo update

Install chart

helm install my-tempo grafana/tempo --version 1.0.2
