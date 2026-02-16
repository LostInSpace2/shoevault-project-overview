# ShoeVault – Cloud-Native DevOps Project

## 📌 Project Overview
ShoeVault is a cloud-native application designed and deployed using modern DevOps practices.  
The project demonstrates containerization, CI/CD automation, and Kubernetes-based deployment on AWS.

---

## 🏗 Architecture
## Architecture (Mermaid)
```mermaid
flowchart TB
    User[External User]

    subgraph AWS[AWS Account - Region: ap-south-1]
        ECR[Amazon ECR<br/>987277323853.dkr.ecr.ap-south-1.amazonaws.com/shoevault-api]

        subgraph EKS[EKS Cluster]
            NG[Node Group / Worker Nodes]

            subgraph NS[Namespace: shoevault]

                %% Layer 1: Entry
                subgraph ENTRY[Entry]
                    SVC_LB[Service: shoevault-api<br/>Type: LoadBalancer<br/>External DNS]
                end

                %% Layer 2: Controllers
                subgraph CTRL[Controllers]
                    DEP[Deployment: shoevault-api<br/>Replicas: 2]
                    STS[StatefulSet: postgres<br/>Replicas: 1]
                end

                %% Layer 3: Runtime
                subgraph RT[Runtime]
                    APIPODS[(API Pods x2)]
                    DBPOD[(Postgres Pod)]
                end

                %% Layer 4: Services
                subgraph SVC[Internal Services]
                    DB_SVC[Service: postgres<br/>Type: ClusterIP]
                end

                %% Layer 5: Storage (K8s)
                subgraph STORAGE[Storage]
                    PVC[PersistentVolumeClaim<br/>postgres-data]
                end

            end

            %% Storage (AWS)
            EBS[(EBS Volume)]
        end
    end

    %% Traffic flow
    User -->|HTTP| SVC_LB
    SVC_LB --> APIPODS

    %% Controllers create/manage runtime
    DEP --> APIPODS
    STS --> DBPOD

    %% DB connectivity
    APIPODS -->|SQL 5432| DB_SVC
    DB_SVC --> DBPOD

    %% Persistence
    DBPOD --> PVC
    PVC --> EBS

    %% Image source (side path)
    NG -->|Pull image| ECR
```

## CI/CD Workflow (Mermaid)
```mermaid
flowchart LR
    Dev[Developer]

    subgraph GitHub[GitHub]
        Repo[shoevault-app Repository]

        subgraph Actions[GitHub Actions Workflow]
            subgraph CI[CI Stages]
                Unit[Unit Tests - SQLite]
                Int[Integration Tests - PostgreSQL]
                Build[Build and Tag Docker image]
                Login[Login to ECR]
            end
            Push[Push image to ECR]
            Deploy[kubectl set image]
        end
    end

    subgraph AWS[AWS Account - Region: ap-south-1]
        ECR[Amazon ECR<br/>987277323853.dkr.ecr.ap-south-1.amazonaws.com/shoevault-api]

        subgraph EKS[EKS Cluster]
            DEP[Deployment: shoevault-api]
            PODS[(API Pods)]
            LB[Service: shoevault-api<br/>Type: LoadBalancer<br/>External DNS]
        end
    end

    %% Trigger
    Dev -->|git push| Repo
    Repo -->|triggers| Unit

    %% CI sequence
    Unit --> Int --> Build --> Login --> Push
    Push --> ECR

    %% CD
    Deploy --> DEP
    Actions --> Deploy
    DEP -->|rollout| PODS
    LB -->|routes HTTP traffic| PODS

    %% Runtime pull
    PODS -->|Pull image| ECR
```

High-level flow:
- Flask-based application
- PostgreSQL database
- Docker containerization
- Amazon ECR for image storage
- Amazon EKS for orchestration
- CI pipeline for automated testing and image validation

---

## ⚙️ Tech Stack
- AWS (ECR, EKS)
- Docker
- Kubernetes
- GitHub Actions (CI)
- PostgreSQL
- Python (Flask)

---

## 🚀 Infrastructure & Deployment
- Container images built and pushed to Amazon ECR
- Application deployed to Amazon EKS
- Kubernetes manifests for deployments and services
- CI pipeline for automated tests and Docker image validation

---

## 📂 Project Repositories

Main application:
👉 https://github.com/YOUR_USERNAME/shoevault-app

Infrastructure:
👉 https://github.com/YOUR_USERNAME/shoevault-infra

Cluster resources:
👉 https://github.com/YOUR_USERNAME/shoevault-cluster-resources

---

## 📎 Full Source Code
All components are publicly available in the repositories above.
