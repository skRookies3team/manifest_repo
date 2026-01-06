# manifest_repo

GitOps 방식으로 ArgoCD가 Helm Chart 변경을 감지하여  
AWS EKS(Kubernetes)에 자동으로 반영하는 **Manifest Repository**입니다.

MSA 서비스별 Helm Chart를 기반으로  
일관되고 재현 가능한 배포 환경을 목표로 합니다.


## Repository 역할

- MSA 서비스별 Kubernetes 배포 설정을 Git으로 관리
- ArgoCD를 통한 변경 사항 자동 반영 및 배포 상태 관리
- Helm Chart를 활용한 서비스 단위 배포 구성



## Architecture Overview

```text
┌──────────────────────────┐
│     Application Repo     │
│     (Backend Service)    │
└─────────┬────────────────┘
          │
          │  main branch git push
          ▼
┌──────────────────────────┐
│     GitHub Actions       │
│  - Build JAR             │
│  - Build Docker Image    │
│  - Push to DockerHub     │
└─────────┬────────────────┘
          │
          │  image tag update
          ▼
┌────────────────────────────────┐
│   GitHub - manifest_repo       │
│   (Helm Chart / values.yaml)   │
└─────────┬──────────────────────┘
          │
          │  Git 변경 감지
          ▼
┌────────────────────────────────┐
│            ArgoCD              │
│  - Render Helm Chart           │
│  - Sync                        │
└─────────┬──────────────────────┘
          │
          │  Sync to Cluster
          ▼
┌────────────────────────────────┐
│        AWS EKS Cluster         │
│     (Kubernetes on AWS)        │
│  - Deployment / Service / Pod  │
└────────────────────────────────┘