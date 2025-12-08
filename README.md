# k8s-local-test
k8s local test via minikube

# Install minikube
```
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-darwin-arm64

sudo install minikube-darwin-arm64 /usr/local/bin/minikube
```

# Install kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"

sudo mv kubectl /usr/local/bin/
```

# Run
```
minikube start
```