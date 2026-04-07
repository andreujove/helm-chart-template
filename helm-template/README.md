
# Components:
- HTTP/HTTPS Echo Service (mendhak/http-https-echo)

## Prerequisites:

1. Install minikube:
```bash
curl -LO https://github.com/kubernetes/minikube/releases/download/v1.36.0/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```
Verify installation:
```bash
minikube version
```


2. Install kubectl:
```bash
curl -LO "https://dl.k8s.io/release/v1.34.1/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/kubectl
```
- Verify installation:
```bash
kubectl version
```

3. Install helm:

```bash
curl -LO https://get.helm.sh/helm-v3.19.2-linux-amd64.tar.gz
tar -zxvf helm-v3.19.2-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
```


## Installation:


1. Start a minikube cluster:

```bash
minikube start -p test-helm --kubernetes-version=v1.33.0 --cpus=4 --memory=4096
```

2. Check the nodes:
```bash
kubectl get nodes
```

3. Activate profile of minikube:
```bash
minikube profile test-helm
```

4. To expose Services type LoadBalancer use:
```bash
minikube tunnel
```

5. Create ns:
```bash
kubectl create ns test-http-echo

```

6. Metrics-server setup:
```bash

helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/

helm search repo metrics-server/metrics-server --versions

# ONLY for local development (skips TLS verification of nodes)
helm upgrade --install metrics-server metrics-server/metrics-server \
  --namespace kube-system \
  --version 3.13.0 \
  --set args={--kubelet-insecure-tls,--kubelet-preferred-address-types=InternalIP} \
  --wait

helm upgrade --install metrics-server metrics-server/metrics-server \
  --namespace kube-system \
  --version 3.13.0 \
  --set args={--kubelet-preferred-address-types=InternalIP} \
  --wait

```

7. Install Helm:
```bash
helm upgrade --install test-release . --values ./values.yaml --debug --wait --timeout 1800s --namespace test-http-echo

helm history test-release

kubectl port-forward service/test-release 8080:80 --namespace test-http-echo

```

8. Testing HPA:
```bash

# TEST CPU:
kubectl exec -it <POD_NAME> -- /bin/sh -c "while true; do sha1sum /dev/zero; done"

# TEST Memory:
kubectl exec -it <POD_NAME> -- /bin/sh -c "set -e; data=\$(head -c 100M /dev/zero | tail); echo 'Memory allocated. Sleeping for 5m...'; sleep 300"

```


9. Delete minikube:
```bash

helm uninstall test-release --namespace test-http-echo

minikube delete -p test-istio
```