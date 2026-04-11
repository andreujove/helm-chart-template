# ☸️ Helm Chart Template

A standardized, highly configurable Helm chart designed for deploying containerized applications to Kubernetes with best practices. This template provides production-ready configurations for multiple environments with automatic scaling, resource policies, and comprehensive monitoring support.

## Table of Contents

- [Quick Start](#quick-start)
- [Features](#features)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Environment Configurations](#environment-configurations)
- [Usage Examples](#usage-examples)
- [Documentation](#documentation)

## Quick Start

Get up and running in minutes:

```bash
# 1. Install prerequisites (see Prerequisites section)
# 2. Start a local Kubernetes cluster
minikube start -p test-helm --kubernetes-version=v1.33.0 --cpus=4 --memory=4096

# 3. Create namespace
kubectl create ns test-http-echo

# 4. Deploy the Helm chart
helm upgrade --install my-release ./helm-template \
  --namespace test-http-echo \
  --values ./helm-template/values.yaml \
  --wait

# 5. Access your application
kubectl port-forward service/my-release 8080:80 --namespace test-http-echo
# Visit http://localhost:8080
```

## Features

- **Flexible Deployment**: Configurable replicas, resource requests/limits, and image settings
- **Horizontal Pod Autoscaling (HPA)**: Automatic scaling based on CPU and memory metrics
- **Pod Disruption Budget (PDB)**: Ensures high availability during node maintenance
- **Service Discovery**: Automatic DNS service discovery and load balancing
- **RBAC Support**: Service account configuration for secure access
- **Multi-Environment Support**: Separate configurations for dev and prod environments
- **ConfigMap Management**: Centralized configuration for applications
- **Best Practices**: Production-ready security and resource configurations

## Project Structure

```
helm-chart-template/
├── README.md                  # This file
├── helm-template/
│   ├── Chart.yaml            # Helm chart metadata
│   ├── README.md             # Detailed Helm chart documentation
│   ├── values.yaml           # Default configuration values
│   ├── templates/            # Kubernetes resource templates
│   │   ├── configmap.yaml
│   │   ├── deployment.yaml
│   │   ├── hpa.yaml         # Horizontal Pod Autoscaler
│   │   ├── pdb.yaml         # Pod Disruption Budget
│   │   ├── service.yaml
│   │   ├── service-account.yaml
│   │   └── NOTES.txt        # Post-install instructions
│   └── values/              # Environment-specific overrides
│       ├── dev/
│       │   └── eu-central-1/
│       └── prod/
│           └── eu-central-1/
```

## Prerequisites

### System Requirements

- **Kubernetes**: v1.33.0 or higher
- **Helm**: v3.19.2 or higher
- **kubectl**: v1.34.1 or higher
- **Docker**: For building and running container images
- **minikube** (optional): For local development and testing

### Installation Instructions

For detailed installation steps and setup instructions, see [helm-template/README.md](helm-template/README.md).

## Installation

### 1. Basic Deployment

Deploy with default values:

```bash
helm upgrade --install my-app ./helm-template \
  --namespace default \
  --wait
```

### 2. Dev Environment Deployment

Deploy to development with environment-specific values:

```bash
helm upgrade --install my-app ./helm-template \
  --namespace dev \
  --values ./helm-template/values.yaml \
  --values ./helm-template/values/dev/eu-central-1/values.yaml \
  --wait
```

### 3. Production Environment Deployment

Deploy to production with environment-specific values:

```bash
helm upgrade --install my-app ./helm-template \
  --namespace prod \
  --values ./helm-template/values.yaml \
  --values ./helm-template/values/prod/eu-central-1/values.yaml \
  --wait \
  --timeout 5m
```

### 4. Verify Deployment

```bash
# Check release status
helm status my-app --namespace default

# View release history
helm history my-app --namespace default

# Check pod status
kubectl get pods --namespace default
```

## Environment Configurations

The `values/` directory contains environment-specific configurations:

- **`values/dev/eu-central-1/`**: Development environment settings for EU Central region
- **`values/prod/eu-central-1/`**: Production environment settings for EU Central region

Customize these values files for your specific environment needs (region, scaling policies, resource limits, etc.).

## Usage Examples

### Check Chart Values

```bash
# View default values
helm show values ./helm-template

# View values with environment overrides
helm show values ./helm-template \
  --values ./helm-template/values/prod/eu-central-1/values.yaml
```

### Upgrade Deployment

```bash
helm upgrade my-app ./helm-template \
  --namespace default \
  --values ./helm-template/values/dev/eu-central-1/values.yaml \
  --wait
```

### Rollback Deployment

```bash
# Rollback to previous release
helm rollback my-app 1 --namespace default
```

### Delete Deployment

```bash
helm uninstall my-app --namespace default
```

## Documentation

- [Helm Chart Details](helm-template/README.md) - Detailed setup instructions, component information, and troubleshooting
- [Official Helm Documentation](https://helm.sh/docs/)
- [Kubernetes Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)

## License

GNU General Public License v3.0 (GPL v3)

Copyright (c) 2026

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program. If not, see <http://www.gnu.org/licenses/>.

### GPL v3 Summary:

**You can:**
- ✅ Use the software for any purpose
- ✅ Modify the software
- ✅ Distribute the software

**You must:**
- ✔️ Include the license and copyright notice
- ✔️ Disclose the source code
- ✔️ State significant changes to the code
- ✔️ Use the same license (GPL v3) for any derivative works

**Key Principle:** Any software derived from this must also be GPL v3 licensed (Copyleft)

For the full license text, visit: [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.html)

## Contributing

[Add contribution guidelines here]
