# RabbitMQ Helm Chart

A Helm chart for deploying RabbitMQ 4.1 on Kubernetes with management plugin enabled.

## Introduction

This chart bootstraps a RabbitMQ deployment on a Kubernetes cluster using the Helm package manager.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- PV provisioner support in the underlying infrastructure (if persistence is enabled)

## Installing the Chart

To install the chart with the release name `my-rabbitmq`:

```bash
helm install my-rabbitmq .
```

## Uninstalling the Chart

To uninstall/delete the `my-rabbitmq` deployment:

```bash
helm uninstall my-rabbitmq
```

## Configuration

The following table lists the configurable parameters of the RabbitMQ chart and their default values.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `1` |
| `image.repository` | RabbitMQ image repository | `rabbitmq` |
| `image.tag` | RabbitMQ image tag | `4.1.0-management` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `auth.username` | RabbitMQ admin username | `admin` |
| `auth.password` | RabbitMQ admin password | `changeme` |
| `auth.erlangCookie` | Erlang cookie for clustering | `secretcookie` |
| `service.type` | Kubernetes service type | `ClusterIP` |
| `service.port` | AMQP service port | `5672` |
| `service.managementPort` | Management UI port | `15672` |
| `persistence.enabled` | Enable persistence using PVC | `true` |
| `persistence.storageClass` | PVC Storage Class | `""` |
| `persistence.accessMode` | PVC Access Mode | `ReadWriteOnce` |
| `persistence.size` | PVC Storage Request | `8Gi` |
| `resources.limits.cpu` | CPU limit | `1000m` |
| `resources.limits.memory` | Memory limit | `2Gi` |
| `resources.requests.cpu` | CPU request | `500m` |
| `resources.requests.memory` | Memory request | `1Gi` |
| `livenessProbe.enabled` | Enable liveness probe | `true` |
| `readinessProbe.enabled` | Enable readiness probe | `true` |
| `metrics.enabled` | Enable Prometheus metrics | `false` |
| `ingress.enabled` | Enable ingress | `false` |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example:

```bash
helm install my-rabbitmq . \
  --set auth.password=secretpassword \
  --set persistence.size=20Gi
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart:

```bash
helm install my-rabbitmq . -f values.yaml
```

## Accessing RabbitMQ

### Management UI

The RabbitMQ Management UI is accessible on port 15672. To access it locally:

```bash
kubectl port-forward svc/my-rabbitmq 15672:15672
```

Then open http://localhost:15672 in your browser and login with the configured username and password.

### AMQP Connection

Applications can connect to RabbitMQ on port 5672 using the service name:

```
amqp://<username>:<password>@my-rabbitmq:5672/
```

## Persistence

The RabbitMQ image stores data at the `/var/lib/rabbitmq` path of the container.

By default, the chart mounts a Persistent Volume at this location. The volume is created using dynamic volume provisioning.

## Security

- The default credentials should be changed in production environments
- Use Kubernetes secrets to store sensitive information
- Consider enabling TLS for AMQP and management connections
- The erlang cookie should be unique per cluster
