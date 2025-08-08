
# 🎮 Tetris Game Deployment using EKS + Argo CD (GitOps)

This project demonstrates how to deploy a containerized **Tetris game application** to an **Amazon EKS** cluster using **Argo CD** for GitOps-based continuous delivery.

---

## 🚀 Architecture Overview

- **Cloud Provider**: AWS
- **Cluster**: Amazon EKS
- **GitOps Tool**: Argo CD
- **CI/CD Model**: Push-based (Git triggers Argo CD sync)
- **Service Type**: LoadBalancer (exposes Tetris app publicly)

---

## 📁 Project Structure

```
tetris-argocd/
├── kustomization.yaml
├── tetris-deployment.yaml
├── tetris-service.yaml
├── README.md
```

---

## 🔧 Kubernetes Resources

### 1. **Deployment**

`tetris-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tetris-deployment
  labels:
    app: tetris
spec:
  replicas: 2
  selector:
    matchLabels:
      app: tetris
  template:
    metadata:
      labels:
        app: tetris
    spec:
      containers:
        - name: tetris
          image: <your-dockerhub-or-ecr-image>
          ports:
            - containerPort: 80
```

### 2. **Service**

`tetris-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: tetris-service
spec:
  type: LoadBalancer
  selector:
    app: tetris
  ports:
    - port: 80
      targetPort: 80
```

### 3. **Kustomization**

`kustomization.yaml`

```yaml
resources:
  - tetris-deployment.yaml
  - tetris-service.yaml
```

---

## 🌐 Accessing the Application

After deploying via Argo CD, the service is exposed using an AWS LoadBalancer.

**Public URL**:

```
http://<your-external-dns>
```

Example:
```
http://a79b1b7981f0c4440ba1ef16618d3a99-767608856.us-east-1.elb.amazonaws.com
```

---

## 📦 Argo CD Integration

Argo CD watches this repository and syncs changes automatically or manually.

### Argo CD App Spec (created from UI/CLI):

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: tetris
spec:
  project: default
  source:
    repoURL: https://github.com/<your-user>/tetris-argocd.git
    targetRevision: main
    path: .
  destination:
    server: https://kubernetes.default.svc
    namespace: tetris
  syncPolicy:
    automated: {}
```

---

## ✅ How to Use This Repo

1. Clone this repo
2. Make changes to YAMLs
3. Commit and push
4. Argo CD will auto-sync (or manual sync if disabled)

---

## 📌 Notes

- Container listens on `port: 80`
- Service exposes using `LoadBalancer` (for public access)
- Namespace `tetris` is used
- Initial Argo CD password is pulled from Kubernetes secret

---
**Venkatesh Kesa**  
Reach out if you have questions or want to contribute.
