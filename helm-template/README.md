
# Helm Chart - Detailed Setup Guide

This guide provides step-by-step instructions for setting up, deploying, and testing the Helm chart locally with minikube.

## Table of Contents

- [Components](#components)
- [Prerequisites](#prerequisites)
- [Local Development Setup](#local-development-setup)
- [Helm Chart Deployment](#helm-chart-deployment)
- [Testing & Verification](#testing--verification)
- [Testing Horizontal Pod Autoscaling (HPA)](#testing-horizontal-pod-autoscaling-hpa)
- [Troubleshooting](#troubleshooting)
- [Cleanup](#cleanup)

## Components

- **HTTP/HTTPS Echo Service**: mendhak/http-https-echo
  - A simple echo service for testing HTTP/HTTPS requests
  - Used for validating the deployment and service configuration

## Prerequisites

### System Requirements

- Linux/macOS/WSL2 environment
- 4GB+ available RAM
- Internet connection

### Tools Required

You'll need to install the following tools locally. Choose appropriate versions:

#### 1. Install minikube (v1.36.0)

```bash
curl -LO https://github.com/kubernetes/minikube/releases/download/v1.36.0/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

Verify installation:
```bash
minikube version
```

#### 2. Install kubectl (v1.34.1)

```bash
curl -LO "https://dl.k8s.io/release/v1.34.1/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/kubectl
```

Verify installation:
```bash
kubectl version
```

#### 3. Install Helm (v3.19.2)

```bash
curl -LO https://get.helm.sh/helm-v3.19.2-linux-amd64.tar.gz
tar -zxvf helm-v3.19.2-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
```

Verify installation:
```bash
helm version
```

---

## Local Development Setup

### Step 1: Start minikube Cluster

Start a local Kubernetes cluster with minikube (Kubernetes v1.33.0):

```bash
minikube start -p test-helm \
  --kubernetes-version=v1.33.0 \
  --cpus=4 \
  --memory=4096
```

Options explained:
- `-p test-helm`: Profile name for this cluster
- `--kubernetes-version=v1.33.0`: Kubernetes version to use
- `--cpus=4`: Number of CPUs to allocate
- `--memory=4096`: Memory in MB to allocate

### Step 2: Verify Cluster

Check that the cluster started successfully:

```bash
kubectl get nodes
```

You should see the `test-helm` node in a `Ready` state.

### Step 3: Set Active Profile

Activate the minikube profile:

```bash
minikube profile test-helm
```

### Step 4: Enable minikube tunnel (For LoadBalancer Services)

In a separate terminal, run the tunnel to expose LoadBalancer services:

```bash
minikube tunnel
```

Keep this terminal open while testing. This allows external access to services exposed via LoadBalancer.

### Step 5: Create Kubernetes Namespace

Create a namespace for the application:

```bash
kubectl create ns test-http-echo
```

### Step 6: Install Metrics Server

The Metrics Server is required for HPA (Horizontal Pod Autoscaling) to function. It collects resource metrics from nodes and pods.

Add the metrics-server Helm repository:

```bash
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm repo update
helm search repo metrics-server/metrics-server --versions
```

**For Local Development (skips TLS verification):**

```bash
helm upgrade --install metrics-server metrics-server/metrics-server \
  --namespace kube-system \
  --version 3.13.0 \
  --set args={--kubelet-insecure-tls,--kubelet-preferred-address-types=InternalIP} \
  --wait
```

**For Production (with TLS verification):**

```bash
helm upgrade --install metrics-server metrics-server/metrics-server \
  --namespace kube-system \
  --version 3.13.0 \
  --set args={--kubelet-preferred-address-types=InternalIP} \
  --wait
```

---

## Helm Chart Deployment

### Step 1: Deploy the Chart

Deploy the Helm chart with default values:

```bash
helm upgrade --install test-release . \
  --namespace test-http-echo \
  --values ./values.yaml \
  --debug \
  --wait \
  --timeout 1800s
```

Options explained:
- `upgrade --install`: Install if not exists, otherwise upgrade
- `test-release`: Release name
- `.`: Current directory (chart location)
- `--namespace`: Kubernetes namespace to deploy to
- `--values`: Configuration file
- `--debug`: Show debug output
- `--wait`: Wait for resources to be ready
- `--timeout`: Maximum wait time

### Step 2: Check Release Status

View the release status and history:

```bash
helm status test-release --namespace test-http-echo
helm history test-release --namespace test-http-echo
```

### Step 3: Access the Service

Forward the service port to your local machine:

```bash
kubectl port-forward service/test-release 8080:80 --namespace test-http-echo
```

Now access the application at: **http://localhost:8080**

---

## Testing & Verification

### Check Deployment Status

```bash
# View deployments
kubectl get deployments --namespace test-http-echo

# View pods
kubectl get pods --namespace test-http-echo

# View services
kubectl get svc --namespace test-http-echo

# View replica sets
kubectl get rs --namespace test-http-echo
```

### Check Pod Logs

```bash
# Get pod name
POD_NAME=$(kubectl get pods -n test-http-echo -o jsonpath='{.items[0].metadata.name}')

# View logs
kubectl logs $POD_NAME --namespace test-http-echo
```

### Describe Resources

```bash
# Get detailed pod information
kubectl describe pod <POD_NAME> --namespace test-http-echo

# Get events
kubectl get events --namespace test-http-echo --sort-by='.lastTimestamp'
```

---

## Testing Horizontal Pod Autoscaling (HPA)

### Prerequisites

Ensure metrics-server is running:

```bash
kubectl get deployment metrics-server --namespace kube-system
```

### Get Pod Name

```bash
POD_NAME=$(kubectl get pods -n test-http-echo -o jsonpath='{.items[0].metadata.name}')
echo $POD_NAME
```

### Test CPU Load

In one terminal, start a CPU stress test:

```bash
kubectl exec -it $POD_NAME --namespace test-http-echo -- /bin/sh -c "while true; do sha1sum /dev/zero; done"
```

Watch HPA scale up in another terminal:

```bash
kubectl get hpa --namespace test-http-echo --watch
```

### Test Memory Load

In one terminal, allocate memory:

```bash
kubectl exec -it $POD_NAME --namespace test-http-echo -- /bin/sh -c "set -e; data=\$(head -c 100M /dev/zero | tail); echo 'Memory allocated. Sleeping for 5m...'; sleep 300"
```

Watch HPA in another terminal:

```bash
kubectl get hpa --namespace test-http-echo --watch
```

---

## Troubleshooting

### HPA Not Scaling

**Problem**: HPA stuck at "unknown" metrics

**Solution**: Verify metrics-server is running and collecting metrics:

```bash
# Check metrics-server status
kubectl get deployment metrics-server --namespace kube-system

# Check if metrics are available
kubectl top nodes
kubectl top pods --namespace test-http-echo
```

### Pods Not Starting

**Problem**: Pods stuck in Pending or CrashLoopBackOff state

**Solution**: Check events and logs:

```bash
kubectl describe pod <POD_NAME> --namespace test-http-echo
kubectl logs <POD_NAME> --namespace test-http-echo
kubectl get events --namespace test-http-echo --sort-by='.lastTimestamp'
```

### Service Not Accessible

**Problem**: Cannot access service via port-forward

**Solution**: 

```bash
# Check if service exists
kubectl get svc --namespace test-http-echo

# Check if pods are ready
kubectl get pods --namespace test-http-echo

# Verify port-forward is working
kubectl port-forward service/test-release 8080:80 --namespace test-http-echo
```

---

## Cleanup

### Delete the Helm Release

```bash
helm uninstall test-release --namespace test-http-echo
```

### Delete the Namespace

```bash
kubectl delete ns test-http-echo
```

### Stop minikube

```bash
minikube stop -p test-helm
```

### Delete minikube Cluster

```bash
minikube delete -p test-helm
```

---

## Additional Resources

- [Helm Documentation](https://helm.sh/docs/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [minikube Documentation](https://minikube.sigs.k8s.io/)
- [Metrics Server](https://github.com/kubernetes-sigs/metrics-server)