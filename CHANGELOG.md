# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2025-10-04

### Fixed
- Resolved Erlang distributed node naming incompatibility that caused pods to enter `CrashLoopBackOff` state
  - Root cause: Mismatch between fully qualified domain name (FQDN) configuration and Erlang's short name resolution mode
  - Solution: Introduced `RABBITMQ_USE_LONGNAME` environment variable set to `true` to enable long name mode
  - Impact: StatefulSet pods now successfully initialize with FQDN format: `rabbit@<pod-name>.<headless-service>.<namespace>.svc.cluster.local`
  - Error resolved: `Can't set short node name!` and subsequent `nodistribution` failures

### Changed
- Optimized Helm chart packaging process
  - Introduced `.helmignore` to exclude version control artifacts and build dependencies
  - Reduced chart package size from 90KB to 4.7KB (94.8% reduction)
  - Excluded: `.git/`, `index.yaml`, `*.tgz` files

### Technical Implementation

**Configuration Changes:**
```yaml
env:
  - name: RABBITMQ_USE_LONGNAME
    value: "true"
```

**Chart Metadata:**
- Chart Version: 1.2.0
- Application Version: 4.1.0
- Compatibility: Kubernetes 1.19+

### Migration Guide

#### For New Deployments
Reference version 1.2.0 in your `Chart.yaml`:
```yaml
dependencies:
  - name: rabbitmq
    version: 1.2.0
    repository: https://magefleet.github.io/helm-chart-rabbitmq/
```

#### For Existing Deployments
Update chart dependencies and perform rolling upgrade:
```bash
helm dependency update
helm upgrade <release-name> <chart-path> --namespace <namespace>
```

**Note:** This release is fully backward compatible. No configuration changes or data migration required.

### Compatibility
- **Breaking Changes:** None
- **Deprecations:** None
- **Known Issues:** None

---

## [1.1.0] - 2025-10-03

### Changed
- Updated RabbitMQ image to version 4.1.0-management
- Enhanced probe configurations for production readiness

---

## [1.0.0] - 2025-10-02

### Added
- Initial release of RabbitMQ Helm Chart
- Support for StatefulSet deployments with persistent storage
- Configurable resource limits and requests
- Health check probes (liveness and readiness)
- RabbitMQ Management UI integration
- Erlang cookie secret management
- Headless service for cluster communication
