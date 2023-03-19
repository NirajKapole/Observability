# Observability with OpenTelemetry on Kubernetes
Observability is the ability to gain insight into the behavior and performance of a system through its telemetry data, including metrics, logs, and traces. OpenTelemetry is a vendor-neutral, open-source framework that provides a standardized way to instrument, collect, and export telemetry data from distributed systems.

This document will guide you through the process of setting up observability using OpenTelemetry, including installing cert-manager, Helm, OpenTelemetry, and Tempo, as well as creating YAML files and deploying applications.

Index Topics
| Index | Content                                                                                    |
|-------|--------------------------------------------------------------------------------------------|
| 1     | Cert-manager - What it is and how to install it on a Kubernetes cluster                     |
| 2     | Helm - What it is, Helm-charts, and how to install it on a Kubernetes cluster                |
| 3     | OpenTelemetry - What it is and how to install it on a Kubernetes cluster using Helm charts |
| 4     | OpenTelemetry Collector and Sidecar mode - How to create YAML files for both                |
| 5     | Node app and Java app deployment using OpenTelemetry                                        |
| 6     | Tempo - What it is and how to install it using a Helm chart                                 |


# What is cert-manager? and Installation on kubernetes cluster.
Cert-manager is a Kubernetes add-on that automates the management and issuance of TLS certificates from various certificate authorities (CAs). It provides an easy way to secure your Kubernetes applications by automatically obtaining and renewing TLS certificates.

You can install cert-manager via [cert-manager](https://cert-manager.io/docs/installation/kubectl/). More information is available [here](https://cert-manager.io/docs/).

Update the Helm repository:
      
```helm repo update```

# What is Helm? What are Helm-charts and Installation on kubernetes cluster. 
Helm is a package manager for Kubernetes that allows you to easily install, manage, and upgrade applications on your cluster. Helm-charts are preconfigured packages that contain all the resources needed to deploy an application on Kubernetes.

To install Helm on your Kubernetes cluster, follow these steps:

Download the Helm binary from the official Helm website:

```$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3```

```$ chmod 700 get_helm.sh```

```$ ./get_helm.sh```


# What is OpenTelemetry & Installation on k8s cluster using Helm-charts.
OpenTelemetry is a framework that allows you to instrument your applications to collect telemetry data such as traces, metrics, and logs. It provides a vendor-neutral, open-source way to collect telemetry data from distributed systems.

To install OpenTelemetry on your Kubernetes cluster using Helm charts, follow these steps:

Add the OpenTelemetry Helm repository:

```helm repo add open-telemetry https://opentelemetry.github.io/opentelemetry-helm-charts```

Update the Helm repository:

```helm repo update```

# Configuring Opentelemetry YAML file

# Configuring app.yaml file as a deployment using OpenTelemetry.

# What is Tempo? and Installation using Helm-chart.
Tempo is an open-source, easy-to-use, and scalable distributed tracing backend that supports the OpenTelemetry standard. It allows you to store and analyze your traces in a cost-effective way.

To install Tempo on your Kubernetes cluster using Helm charts, follow these steps:

Add repository

```helm repo add grafana https://grafana.github.io/helm-charts```

Update repository

```helm repo update```

Install chart

```helm install my-tempo grafana/tempo --version 1.0.2```





