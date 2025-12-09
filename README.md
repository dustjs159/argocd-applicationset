ArgoCD Applicationset  
========================

Setup argocd applicationset via minikube

## Install minikube
```
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-darwin-arm64

sudo install minikube-darwin-arm64 /usr/local/bin/minikube
```

## Install kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"

sudo mv kubectl /usr/local/bin/
```

## Run
```
minikube start
```

# Setup

## Bootstrap (최초 1회, 클러스터 관리자 수행)

### 1. ArgoCD 설치
```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm install argocd argo/argo-cd -n argocd --create-namespace \
  -f charts/argocd/values.yaml
```

### 2. ArgoCD 접속
```bash
# Admin 비밀번호 확인
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d

# Port-forward
kubectl port-forward svc/argocd-server -n argocd 8080:443

# 로그인
argocd login localhost:8080
```

### 3. GitHub Repository 등록
```bash
# 1Password/Bitwarden에서 GitHub Token 가져오기
# op read "op://DevOps/GitHub/ArgoCD Token"
# bw get password "GitHub ArgoCD Token"

export GITHUB_TOKEN="ghp_xxxx"  # 금고에서 가져온 토큰

argocd repo add https://github.com/dustjs159/argocd-applicationset.git \
  --username git \
  --password $GITHUB_TOKEN \
  --name gitops-main

unset GITHUB_TOKEN  # 즉시 제거
```

### 4. ArgoCD Application 등록
```bash
kubectl apply -f charts/argocd/bootstrap.yaml
```

### 5. 확인
```bash
argocd app list
kubectl get application -n argocd
```

## 운영 (일반 개발자)

새로운 addon 추가:
```bash
mkdir -p charts/external-dns
echo "provider: aws" > charts/external-dns/values.yaml
git push
# ApplicationSet이 자동으로 감지하여 배포
```

기존 addon 수정:
```bash
vim charts/harbor/values.yaml
git push
# 자동 sync
```
