# 🚀 AWS Pipeline GitOps

Bu repository, [aws-pipeline](https://github.com/onurglr/aws-pipeline) ana projesinin GitOps deployment manifest'lerini içerir.

## 📁 İçerik

- **`deployment.yaml`** - Kubernetes Deployment manifest
- **`service.yaml`** - Kubernetes Service manifest  
- **`Jenkinsfile`** - GitOps pipeline (deployment tag güncelleme)

## 🔄 GitOps Workflow

1. Ana projede yeni image build edilir
2. Jenkins pipeline deployment tag'ini günceller
3. ArgoCD otomatik olarak değişiklikleri algılar
4. Kubernetes cluster'a yeni versiyon deploy edilir

## 🛠️ Kullanım

```bash
# Deployment'ı manuel olarak güncelle
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Status kontrol
kubectl get pods -l app=aws-pipeline-cd
kubectl get svc aws-pipeline-cd
```

## 🔗 Bağlantılar

- **Ana Proje**: [aws-pipeline](https://github.com/onurglr/aws-pipeline)
- **Docker Image**: `onurguler18/devopspipeline-aws`
- **Port**: 8080 (NodePort: 31824)

---
*Bu repository GitOps pattern'i kullanarak otomatik deployment sağlar.*
