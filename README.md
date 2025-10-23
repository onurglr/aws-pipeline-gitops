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

## 🔄 GitOps Workflow Diyagramı

### **Genel Workflow:**

```mermaid
graph TB
    subgraph "Ana Proje (aws-pipeline)"
        A[👨‍💻 Developer Push] --> B[🔧 Jenkins Pipeline]
        B --> C[📦 Maven Build]
        C --> D[🧪 Unit Tests]
        D --> E[🐳 Docker Build]
        E --> F[📤 Push to DockerHub]
        F --> G[🚀 Trigger GitOps Pipeline]
    end
    
    subgraph "GitOps Repository (aws-pipeline-gitops)"
        H[📁 deployment.yaml]
        I[📁 service.yaml]
        J[📁 Jenkinsfile]
        G --> K[🔄 Update Image Tag]
        K --> L[📝 Git Commit & Push]
    end
    
    subgraph "Kubernetes Cluster"
        M[⚙️ ArgoCD Controller]
        N[🔄 Auto Sync]
        O[🚀 Deploy to K8s]
        P[📊 Production Pods]
    end
    
    subgraph "External Services"
        Q[🐳 DockerHub Registry]
        R[📋 GitHub Repository]
    end
    
    %% Ana proje akışı
    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    
    %% GitOps akışı
    G --> K
    K --> L
    L --> R
    
    %% ArgoCD akışı
    R --> M
    M --> N
    N --> O
    O --> P
    
    %% Registry bağlantısı
    F --> Q
    O --> Q
    
    %% Styling
    classDef devOps fill:#e1f5fe
    classDef gitOps fill:#f3e5f5
    classDef k8s fill:#e8f5e8
    classDef external fill:#fff3e0
    
    class A,B,C,D,E,F,G devOps
    class H,I,J,K,L gitOps
    class M,N,O,P k8s
    class Q,R external
```

### **ArgoCD ↔ GitOps ↔ Jenkins Detaylı İlişki:**

```mermaid
sequenceDiagram
    participant Dev as 👨‍💻 Developer
    participant Main as 📁 Main Repo
    participant Jenkins as 🔧 Jenkins
    participant DockerHub as 🐳 DockerHub
    participant GitOps as 📋 GitOps Repo
    participant ArgoCD as ⚙️ ArgoCD
    participant K8s as ☸️ Kubernetes

    Dev->>Main: git push code
    Main->>Jenkins: Webhook trigger
    Jenkins->>Jenkins: Build & Test
    Jenkins->>DockerHub: Push image:tag
    Jenkins->>GitOps: Trigger pipeline
    Note over Jenkins,GitOps: Jenkins calls GitOps Jenkinsfile
    GitOps->>GitOps: Update deployment.yaml
    GitOps->>GitOps: git commit & push
    Note over ArgoCD: ArgoCD monitors GitOps repo
    ArgoCD->>ArgoCD: Detect git changes
    ArgoCD->>K8s: Apply new manifests
    K8s->>K8s: Rolling update pods
    ArgoCD->>Jenkins: Sync status (optional)
```

### **ArgoCD, GitOps ve Jenkins Bağlantı Detayları:**

```mermaid
graph LR
    subgraph "Jenkins Environment"
        J1[🔧 Main Pipeline]
        J2[🚀 GitOps Pipeline]
        J3[📊 Build Status]
    end
    
    subgraph "GitOps Repository"
        G1[📁 deployment.yaml]
        G2[📁 service.yaml]
        G3[📝 Git History]
        G4[🏷️ Image Tags]
    end
    
    subgraph "ArgoCD Controller"
        A1[👁️ Git Monitor]
        A2[🔄 Sync Engine]
        A3[📊 App Status]
        A4[🎯 Desired State]
    end
    
    subgraph "Kubernetes Cluster"
        K1[☸️ API Server]
        K2[🚀 Pods]
        K3[🌐 Services]
        K4[📊 Current State]
    end
    
    %% Jenkins to GitOps
    J1 -->|Trigger| J2
    J2 -->|Update| G1
    J2 -->|Commit| G3
    J3 -->|Status| J2
    
    %% GitOps to ArgoCD
    G3 -->|Webhook| A1
    A1 -->|Detect| A2
    A2 -->|Compare| A4
    
    %% ArgoCD to Kubernetes
    A2 -->|Apply| K1
    K1 -->|Create| K2
    K1 -->|Create| K3
    K4 -->|Feedback| A3
    
    %% Status feedback
    A3 -->|Sync Status| J3
    
    %% Styling
    classDef jenkins fill:#ff6b6b
    classDef gitops fill:#4ecdc4
    classDef argocd fill:#45b7d1
    classDef k8s fill:#96ceb4
    
    class J1,J2,J3 jenkins
    class G1,G2,G3,G4 gitops
    class A1,A2,A3,A4 argocd
    class K1,K2,K3,K4 k8s
```

## 🎯 GitOps Repository'sinin Rolü

### **Ana Proje (aws-pipeline):**
- **Code Development**: Spring Boot uygulaması
- **CI Pipeline**: Build, test, Docker image
- **Artifact Creation**: Docker image oluşturma

### **GitOps Repository (aws-pipeline-gitops):**
- **Deployment Configs**: Kubernetes manifest'leri
- **Image Tag Management**: Otomatik tag güncelleme
- **GitOps Trigger**: Ana projeden tetiklenen pipeline

### **Kubernetes Cluster:**
- **ArgoCD**: Git değişikliklerini izler
- **Auto Deployment**: Otomatik sync ve deploy
- **Production**: Canlı ortam

### **Workflow Akışı:**

1. **👨‍💻 Developer** → Code push
2. **🔧 Jenkins** → Build, test, Docker
3. **🐳 DockerHub** → Image push
4. **🚀 GitOps Pipeline** → Tag güncelleme
5. **📝 Git** → Manifest commit
6. **⚙️ ArgoCD** → Otomatik algılama
7. **🚀 Kubernetes** → Production deploy

---
*Bu repository GitOps pattern'i kullanarak otomatik deployment sağlar.*
