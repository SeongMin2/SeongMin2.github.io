---
title: "GCP Ai platform pipeline 배포 및 instance에서 SDK 연결 개요"
categories:
  - mlops
excerpt: "The outline of GCP Ai platform pipeline deployment"
last_modified_at: 2022-03-20
tags:
  - devops
  - mlops
toc: true
toc_sticky: true
---
## 개요

- 처음에는 Local computer에서 kubeflow를 구축을 했음
    - Kubeflow를 구축하려면 Kubernetes가 요구되고, Kubernetes를 구축하려면 Docker 환경이 요구되고, Docker을 구축하려면 Linux OS가 요구됨
    - 필자는 host의 OS가 Window이며, wsl을 사용하여 Docker에 kubernetes를 활성화하여 진행하였음
    - WSL에서 Kubeflow를 구축하게 되면 kubeflow의 containers가 많이 요구되고 wsl2가 컴퓨터의 모든 가용 메모리를 잡아먹어버려서 컴퓨터가 많이 느려짐
        - vmmem 프로세서가 엄청나게 잡아먹음
    - 이를 해결하고자, wls2에 대해 메모리를 강제 할당하여 문제를 어느정도 잡았지만, 이런식으로 진행된다면, 추후 모델 파이프라인이 돌아가는데 큰 걸림돌이 되어 문제가 발생함
- 생각을 해보니, 내가 사용하려는 것은 kubeflow pipeline만 사용하려고 했는데, kubeflow의 모든 components에 대한 container가 구축되어 느려질 수 밖에 없었음
- 찾아보니 kubeflow pipeline만 따로 설치할 수 있는 standalone 방법이 있음
    
    ([https://www.kubeflow.org/docs/components/pipelines/installation/standalone-deployment/](https://www.kubeflow.org/docs/components/pipelines/installation/standalone-deployment/) )
    
- Local에서 standalone으로 시도하려고 했다가, local 컴퓨터의 성능이 좋지 않다고 판단하여 GCP를 활용할 생각을 함
- GCP의 GKE 환경을 구축한 후, 직접 kubeflow pipeline의 standalone deployment를 구축하려고 했는데, GCP에 Ai platform의 pipeline으로 kubeflow pipeline만 standalone 방법으로 잘 갖춰져 있었고, kubeflow는 pipeline만 사용하려는 입장에서 Ai platform을 사용하는것이 좋다고 생각함

## GCP에서의 GKE, AI platform pipeline 개념

앞서 Kubeflow의 개념을 설명했고, 이번에는 Kubeflow가 GCP에서 어떤식으로 진행되는지 실습 설명을 하기 전에 간단하게 설명하겠습니다.

### GKE

- Google Kluster engine으로 Kubernetes의 cluster을 구축할 수 있는 engine
- Container 애플리케이션 배포를 위한 관리형 환경으로 이를 통해서 개발자 생산성, 리소스 효율성, 자동화 된 작업, 오픈소스 유연성을 얻을 수 있음

![Untitled](/assets/post_images/2022-03-20/Untitled.png)

([https://cloud.google.com/architecture/distributed-load-testing-using-gke](https://cloud.google.com/architecture/distributed-load-testing-using-gke))

### AI platform - pipeline

- Pipeline을 구축하는데 kubeflow pipeline을 구성할 수 있도록 제공해줌
- 해당 pipeline은 kubeflow pipeline이므로, 새로운 인스턴스를 만들면, cluster 환경이 요구됨
    - 구축 과정에서 기존의 cluster을 사용해도되고, 새로운 cluster을 구축 과정에서 만들 수 있음
- AI platform pipeline을 위한 클러스터를 구축하려면 최소한의 node와 node의 성능 조건이 요구됨
    - 해당 node는 GCP에서의 VM instance

![Untitled](/assets/post_images/2022-03-20/Untitled%201.png)

( [https://cloud.google.com/ai-platform/pipelines/docs/setting-up](https://cloud.google.com/ai-platform/pipelines/docs/setting-up) ) 

- GKE로 ai platform pipeline을위한 새로운 cluster을 구축하면 위의 조건을 만족하는 node, 즉 vm instance 3개가 자동으로 cluster 안에 생성됨

→ 여기까지 cluster의 구축 완료

- 이후 나머지 pipeline 과정 진행
- 그렇다면 이 pipeline은 해당 cluster에서 어떻게 동작이 되는것인가?
    - cluster 안에 구축된 node들에 최소조건이 존재하는 이유는 node가 components를 실행할 pods를 운영하는데 문제 없게 하기 위한 최소 성능 조건이 필요하기 때문
    - 추후 kubeflow sdk를 이용하여 pipeline components의 application 코드를 짜면 구축한 cluster의 node(vm instance)안의 pods에 한 components 즉 한 container 씩 올려서 실행 되는 것임