
# Components:
- HTTP/HTTPS Echo Service (mendhak/http-https-echo)

## Prerequisites:

1. Install minikube:
```
curl -LO https://github.com/kubernetes/minikube/releases/download/v1.36.0/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```
Verify installation:
```
minikube version
```


2. Install kubectl:
```
curl -LO "https://dl.k8s.io/release/v1.34.1/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/kubectl
```
- Verify installation:
```
kubectl version
```

3. Install helm

```
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

5. Create ns and install the app using the helm & debug:
```bash
kubectl create ns test-http-echo

helm upgrade --install test-release . --values ./values.yaml --debug --wait --timeout 1800s --namespace test-http-echo

helm history test-release

kubectl port-forward service/test-release 8080:8080 --namespace test-http-echo

```

7. Delete minikube:
```bash
minikube delete -p test-istio
```