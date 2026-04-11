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

For detailed setup instructions including all commands and prerequisites, see [helm-template/README.md](helm-template/README.md).

Basic workflow:
1. Install prerequisites (Kubernetes, Helm, kubectl)
2. Configure your values in `values.yaml`
3. Deploy: `helm install my-app ./helm-template`

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

For step-by-step installation instructions with all commands, see [helm-template/README.md](helm-template/README.md).

The chart supports multiple deployment scenarios:
- **Basic Deployment**: Quick setup with default values
- **Dev Environment**: Development-specific configurations
- **Production Environment**: Production-ready configurations with proper resource limits and scaling

Each environment can have region-specific overrides in the `values/` directory.

## Environment Configurations

The `values/` directory contains environment-specific configurations:

- **`values/dev/eu-central-1/`**: Development environment settings for EU Central region
- **`values/prod/eu-central-1/`**: Production environment settings for EU Central region

Customize these values files for your specific environment needs (region, scaling policies, resource limits, etc.).

## Usage Examples

For detailed usage examples with commands, see [helm-template/README.md](helm-template/README.md).

Common operations:
- View chart values
- Deploy to different environments
- Verify deployment status
- Upgrade deployments
- Rollback to previous versions
- Delete deployments

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

This project is maintained by **Andreu Jové**.

For questions or suggestions, please open an issue or contact the maintainer directly.
