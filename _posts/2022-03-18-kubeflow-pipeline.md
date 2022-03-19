---
title: "Kubeflow-pipeline"
categories:
  - mlops
excerpt: "About kubeflow pipeline"
last_modified_at: 2022-03-18
tags:
  - devops
  - mlops
toc: true
toc_sticky: true
---
# Kubeflow

### Definition

- Kubernetes의 ML tool-kit
- Kubernetes 환경에서 ML workflow를 배포할 수 있게 해주는 시스템

### Architecture

![Untitled](/assets/post_images/2022-03-18/Untitled.png)

- Kubernetes 시스템 위에 build 되어 있으며 deploying, scaling 그리고 복잡한 systems를 관리하는데 사용
- Components
    - Central Dashboard
    - Kubeflow Notebooks
    - Kubeflow Pipelines
    - Katib
    - Training Operators
    - Multi-Tenancy

이러한 여러 components중 workflow를 담당하는 pipeline에 대해 알아보자

## Kubeflow-pipeline

### Definition

- Kubeflow pipelines는 Docker containers를 기반으로 구축된 portable, scalable ML workflows를 구축하고 전개하기 위한 platform

### Consist of

- User interface
- Multi-step ML workflows를 scheduling하는 engine
- Pipelines와 components를 정의하고 관리하는 SDK
- SDK를 이용하여 system과 상호작용할 수 있는 Notebooks

### Pipeline

- ML workflow로써, workflow안의 모든 componenet를 포함하고 있고, components가 서로 어떠한 관계를 갖는지 graph 형태로 나타냄

![Untitled](/assets/post_images/2022-03-18/Untitled%201.png)

![Untitled](/assets/post_images/2022-03-18/Untitled%202.png)

- **pipeline의 포함 요소**
    - Run하기 위해 요구되는 Inputs(parameters)의 정의
    - 각 component의 inputs과 outputs를 포함하고 있음
- **Pipeline component**
    - Docker image로 packaged된 user code의 self-contained set
    - Pipeline의 one step을 수행함
    - Components들은 data preprocessing, data transformation, model training, testing 등의 용도로 쓰일 수 있음
- Components는 kubernetes의 pods에 의해 실행됨
- **Kuberflow pipeline 실행 과정** (When you run pipeline)
    
    ![Untitled](/assets/post_images/2022-03-18/Untitled%203.png)
    
    1. 시스템은 workflow(pipeline)의 단계들(steps)에 일치시켜 pods를 하나 이상 실행시킴
    2. 해당 pods는 docker containers를 실행시킴
    3. Containers에 contained된 programs(e.g. python code)를 실행함

[참조]

[https://www.kubeflow.org/docs/components/pipelines/concepts/pipeline/](https://www.kubeflow.org/docs/components/pipelines/concepts/pipeline/) 

[https://www.youtube.com/watch?v=_AY8mmbR1o4&list=PLIivdWyY5sqLS4lN75RPDEyBgTro_YX7x&index=5](https://www.youtube.com/watch?v=_AY8mmbR1o4&list=PLIivdWyY5sqLS4lN75RPDEyBgTro_YX7x&index=5) 

위에서 설명한 pipeline component에 대해 좀 더 자세히 알아보자 

## Component

- Docker image로 packaged된 user code의 self-contained set
- Pipeline의 각 component는 독립적으로 실행됨
- 같은 porcess에서 실행되지 않으며, 직접적으로 in-memory data를 공유할 수 없음
- 모든 data piece를 serialize (직렬화)하여 components 사이로 지나가도록 하여 분산된 network에서 data가 흐름
- 당연하게도 downstream component에서는 사용을 위해 deserialize 해야함

### Component code

- 각 component의 code
- **Client code**
    - jobs (작업)을 제출하는 endpoints에서 소통하는 code
    - 우리가 짜는 code
- **Runtime code**
    - 실질적인 job을 수행하고 주로 cluster에서 실행되는 code

### Component definition

- YAML 형태로 있는 component specification은 kubeflow pipelines system에 대한 component를 설명함
- Component definition의 부분
    - Metadata
    - Interface
    - Implementation

([https://www.kubeflow.org/docs/components/pipelines/concepts/component/](https://www.kubeflow.org/docs/components/pipelines/concepts/component/) )

참조 : 

[https://www.kubeflow.org/docs/components/pipelines/introduction/](https://www.kubeflow.org/docs/components/pipelines/introduction/)