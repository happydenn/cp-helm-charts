# KSQL Server Helm Chart

This chart bootstraps a deployment of a Confluent KSQL Server.

This is an example deployment which runs KSQL Server in non-interactive
mode.
The included queries file `queries.sql` is a stub provided to illustrate one possible approach to mounting queries in the server container via ConfigMap.

## Prerequisites

* Kubernetes 1.9.2+
* Helm 2.8.2+
* A healthy and accessible Kafka Cluster

## Developing Environment

* [Pivotal Container Service (PKS)](https://pivotal.io/platform/pivotal-container-service)
* [Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine/)

## Docker Image Source

* [DockerHub -> ConfluentInc](https://hub.docker.com/u/confluentinc/)

## Installing the Chart

### Install along with cp-helm-charts

```console
git clone https://github.com/confluentinc/cp-helm-charts.git
helm install cp-helm-charts
```

To install with a specific name, you can do:

```console
helm install --name my-confluent cp-helm-charts
```

### Install with a existing cp-kafka and cp-schema-registry release

```console
helm install --set cp-zookeeper.url="unhinged-robin-cp-zookeeper:2181",cp-schema-registry.url="lolling-chinchilla-cp-schema-registry:8081" cp-helm-charts/charts/cp-ksql-server
```

### Installed Components

You can use `helm status <release name>` to view all of the installed components.

For example:

```console{%raw}
$ helm status excited-lynx
STATUS: DEPLOYED

RESOURCES:
==> v1/Service
NAME                         TYPE       CLUSTER-IP    EXTERNAL-IP  PORT(S)   AGE
excited-lynx-cp-ksql-server  ClusterIP  10.31.253.70  <none>       8088/TCP  10s

==> v1beta2/Deployment
NAME                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
excited-lynx-cp-ksql-server  1        1        1           0          10s

==> v1/Pod(related)
NAME                                         READY  STATUS  RESTARTS  AGE
excited-lynx-cp-ksql-server-d4848ff94-x5fmn  2/2    Running   1         10s

==> v1/ConfigMap
NAME                                                DATA  AGE
excited-lynx-cp-ksql-server-jmx-configmap           1     10s
excited-lynx-cp-ksql-server-ksql-queries-configmap  1     10s


NOTES:
This chart installs Confluent KSQL Server.

https://docs.confluent.io/current/ksql/docs
```

There are
1. A [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) `excited-lynx-cp-ksql-server` which contains 1 KSQL Server instance [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/): `excited-lynx-cp-ksql-server-d4848ff94-x5fmn`.
1. A [Service](https://kubernetes.io/docs/concepts/services-networking/service/) `excited-lynx-cp-ksql-server` for clients to connect to KSQL Server.
1. A [ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) which contains configuration for Prometheus JMX Exporter.
1. A [ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) which contains SQL queries for the server to run in non-interactive mode.

## Configuration

You can specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```console
helm install --name my-ksql-server -f my-values.yaml ./cp-ksql-server
```

> **Tip**: A default [values.yaml](values.yaml) is provided

### KSQL Server Deployment

The configuration parameters in this section control the resources requested and utilized by the cp-ksql-server chart.

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `replicaCount` | The number of KSQL Server instances. | `1` |

### Image

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `image` | Docker Image of Confluent KSQL Server. | `confluentinc/cp-ksql-server` |
| `imageTag` | Docker Image Tag of Confluent KSQL Server. | `5.0.0` |
| `imagePullPolicy` | Docker Image Tag of Confluent KSQL Server. | `IfNotPresent` |

### Port

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `servicePort` | The port on which the KSQL Server will be available and serving requests. | `8088` |

### Resources

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `resources.requests.cpu` | The amount of CPU to request. | see [values.yaml](values.yaml) for details |
| `resources.requests.memory` | The amount of memory to request. | see [values.yaml](values.yaml) for details |
| `resources.requests.limit` | The upper limit CPU usage for a KSQL Server Pod. | see [values.yaml](values.yaml) for details |
| `resources.requests.limit` | The upper limit memory usage for a KSQL Server Pod. | see [values.yaml](values.yaml) for details |

### JMX Configuration

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `jmx.port` | The jmx port which JMX style metrics are exposed. | `5555` |

### Prometheus JMX Exporter Configuration

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `prometheus.jmx.enabled` | Whether or not to install Prometheus JMX Exporter as a sidecar container and expose JMX metrics to Prometheus. | `true` |
| `prometheus.jmx.image` | Docker Image for Prometheus JMX Exporter container. | `solsson/kafka-prometheus-jmx-exporter@sha256` |
| `prometheus.jmx.imageTag` | Docker Image Tag for Prometheus JMX Exporter container. | `a23062396cd5af1acdf76512632c20ea6be76885dfc20cd9ff40fb23846557e8` |
| `prometheus.jmx.port` | JMX Exporter Port which exposes metrics in Prometheus format for scraping. | `5556` |

### External Access

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `external.enabled` | whether or not to allow external access to KSQL Server | `false` |
| `external.type` | `Kubernetes Service Type` to expose KSQL Server to external | `LoadBalancer` |

## Dependencies

### Schema Registry (optional)

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `cp-schema-registry.url` | Service name of Schema Registry (Not needed if this is installed along with cp-kafka chart). | `""` |
| `cp-schema-registry.port` | Port of Schema Registry Service | `8081` |
