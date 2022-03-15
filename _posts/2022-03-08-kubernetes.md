---
title: "쿠버네티스 (Kubernetes, k8s)"
categories:
  - mlops
excerpt: "About kubernetes"
last_modified_at: 2022-03-08
tags:
  - devops
  - mlops
toc: true
toc_sticky: true
---
## 정의

- Linux 컨테이너 작업을 자동화하는 오픈소스 플랫폼
    - **Linux 컨테이너**란?
        - 시스템의 나머지 부분과 분리된 1개 이상의 프로세스 세트
        - 운영체제 수준의 가상화 기술로 리눅스 커널(OS)을 공유하면서 프로세스를 격리된 환경에서 실행하는 기술
        - VM과 마찬가지로 자체 파일 시스템, CPU 점유율, 메모리, 프로세스 공간 등이 있음
        
        → 동일한 운영 체제 커널을 공유하고 시스템의 나머지 부분으로부터 애플리케이션 프로세스를 격리함
        
        - 호스트 머신에게는 프로세스로 인식되지만, 컨테이너 관점에서는 마치 독립적인 환경을 가진 가상 머신 처럼 보임
        - 하드웨어를 가상화하는 가상 머신과 달리 커널을 공유하는 방식이기 때문에 실행 속도가 빠르고, 성능 상의 손실이 거의 없음
        
        ![Untitled](/assets/post_images/2022-03-08/Untitled.png)
        
        - 종류
            - 시스템 컨테이너
            - 애플리케이션 컨테이너 (Docker은 대표적인 애플리케이션 컨테이너 런타임)

## 특징

- 컨테이너화된 애플리케이션을 배포하고 확장하는데 수동 프로세스가 필요하지 않음
- 쿠버네스트는 신속한 확장을 요구하는 애플리케이션을 호스팅하는 데 이상적인 플랫폼
- 하나의 애플리케이션을 yaml파일만으로 배포할 수 있음
    - Components도 모두 yaml파일 형태로 실행 됨
- 분산 시스템을 탄력적으로 실행하기 위한 프레임 워크 제공
    - **서비스 디스커버리와 로드 밸런싱**
        - DNS이름을 사용하거나 자체 IP주소를 사용하여 컨테이너를 노출할 수 있음
        - 컨테이너에 대한 트래픽이 많으면, 쿠버네티스는 네트워크 트래픽을 로드밸런싱하고 배포하여 배포가 안정적으로 이뤄질 수 있도록 도와줌
    - **스토리지 오케스트레이션**
        - 로컬 저장소, 공용 클라우드 공급자 등과 같이 원하는 저장소 시스템을 자동으로 탑재할 수 있음
    - **자동화된 롤아웃과 롤백**
        - 배포된 컨테이너의 원하는 상태를 서술할 수 있으며 현재 상태를 원하는 상태로, 설정한 속도에 따라 변경할 수 있음
        
        ex) 쿠버네티스를 자동화해서 배포용 새 컨테이너를 만들고, 기존 컨테이너를 제거하고, 모든 리소스를 새 컨테이너에 적용할 수 있음
        
    - **자동화된 빈 패킹(bin packing)**
        - 컨테이너화된 작업을 실행하는데 사용할 수 있는 쿠버네티스 클러스터 노드를 제공함
        - 쿠버네티스 클러스터 노드 : 각 컨테이너가 필요로 하는 CPU와 메모리를 쿠버네티스에게 지시함
        - 쿠베네티스는 컨테이너를 노드에 맞추어 리소를 가장 잘 사용할 수 있도록 해줌
    - **자동화된 복구(self-healing)**
        - 실패한 컨테이너를 다시 시작하고, 컨테이너를 교체하며 ‘사용자 정의 상태 검사’에 응답하지 않는 컨테이너를 죽이고, 서비스 준비가 끝날 때까지 그러한 과정을 클라이언트에게 보여주지 않음
    - **시크릿과 구성 관리**
        - 쿠버네티스를 사용하면 암호, OAuth 토큰 및 SSH 키와 같은 중요한 정보를 저장하고 관리할 수 있음
        - 컨테이너 이미지를 재구성하지 않고 스택 구성에 시크릿을 노출하지 않고도 시크릿 및 애플리케이션 구성을 배포 및 업데이트 할 수 있음

## Basic Structure

### 쿠버네티스 컴포넌트

![Untitled](/assets/post_images/2022-03-08/Untitled%201.png)

- **Kubernetes Cluster**
    - 쿠버네티스를 배포하면 클러스터를 얻음
    - 노드라고 하는 워커 머신의 집합을 말함
- **Node**
    - 컨테이너화된 애플리케이션을 실행함
    - 모든 클러스터는 최소 한 개의 워커노드를 가짐
    - 워커 노드는 애플리케이션의 구성요소인 파드를 호스트함
    - 개별 하드웨어를 지칭함
    
- **Pod**
    - Linux 컨테이너를 하나 이상 모아놓은 것
    - 클러스터에 실행 중인 컨테이너를 말하며, 쿠버네티스 애플리케이션의 최소 단위
    - 쿠버네티스에서 공유 리소스를 나타내는 하나의 집합
    - 쿠버네티스는 직접 컨테이너를 실행하지 않고, 그 대신 pod를 실행하면서, pod속의 각 컨테이너가 동일한 리소스 및 로컬 네트워크를 공유하게 함  
    -> **컨테이너를 이와 같이 그룹화하면 실제로는 어느 정도 분리된 상태더라도 마치 동일한 물리 하드웨어를 공유하는 것처럼 컨테이너끼리 서로 통신할 수 있게 됨**
    - 보통은 1pod에 1 container 넣는것이 일반적
    - Pod는 일시적인 오브젝트로 언제든지 삭제될 수 있으며, down되면 내부 데이터들이 모두 삭제될 수 있음  
    -> **이를 방지하고자 Volume 마운트를 사용 (Pod에 Volume을 마운트)**
- **Control Plane**
    - 컨테이너의 라이프사이클을 정의, 배포, 관리하기 위한 API와 인터페이스들을 노출하는
    컨테이너 오케스트레이션 레이어를 말함
    - 워커 노드와 클러스터 내 파드를 관리

- 프로덕션 환경에서는 일반적으로,
컨테이너 플레인이 여러 컴퓨터에 걸쳐 실행되고,
클러스터는 여러 노드를 실행하므로
내결함성과 고가용성이 제공됨

## Components

### 서비스 (Service)
* Pod 집합에서 실행중인 애플리케이션을 네트워크 서비스로 노출하는 추상화 방법
* 쿠버네티스는 pod에게 고유한 IP 주소와 pod 집합에 대한 단일 DNS명을 부여하고, 그것들 간에 Load-Balancing을 수행할 수 있음
    * Load-Balancing
        * 컴퓨터 네트워크 기술의 일종으로 둘 혹은 셋 이상의 중앙처리장치 혹은 저장장치와 같은 컴퓨터 자원들에게 작업을 나누는 것을 의미함
        * 여러 서버가 분산처리 하는것을 말함


### Deployment
* Pod와 Replicaset에 대한 선언과 업데이트를 관리해주는 모듈  
-> pod와 Replicaset을 효율적으로 관리하기 위한 모듈
    * Replicaset  
        * 동일 pod에 대한 가용성을 안정적으로 보장받기 위한 모듈


### Job
* 하나 이상의 pod를 지정하고 지정된 수의 pod를 성공적으로 종료될 때까지 계속해서 pod의 실행을 재시도함  
-> Pod와 다른점은 종료되는것을 기대하고 작업 실행결과를 알려줌
* Pod를 이용해서 일회성 또는 정기적인 작업을 실행할 때 사용


### Volume
* Pod에 종속되는 디스크
* Container단위가 아닌 Pod 단위임 (Pod에 속해 있는 여러 컨테이너가 공유 사용 가능)
* 종류
    * emptyDir  
        * 임시 저장소로, Pod가 삭제될 때 같이 삭제됨
        * Pod내 container가 재시작 혹은 문제가 생기더라도 pod 단위이기에 사용가능
    * hostPath
        * 노드의 local disk 경로를 pod에 마운트해서 사용
        * 같은 hostPath에 볼륨은 여러 pod 사이에서 공유되어 사용
    * gitRepo
        * 볼륨 생성 시 지정된 git 레파지토리의 특정 내용을 clone을 이용해서 내려 받은 후, 디스크 볼륨을 생성하는 방식
        * 물리적으로 emptyDir이 생성되고, git clone으로 내용을 다운로드함



## Others
### Kubernetes vs Minikube
* 쿠버네티스는 **Open source** orchestration system
* Mini는 **Local** kubernetes engine



참조 : 

[https://kubernetes.io/ko/docs/concepts/overview/what-is-kubernetes/](https://kubernetes.io/ko/docs/concepts/overview/what-is-kubernetes/)  

[https://www.44bits.io/ko/keyword/linux-container](https://www.44bits.io/ko/keyword/linux-container)  

[https://www.redhat.com/ko/topics/containers/whats-a-linux-container](https://www.redhat.com/ko/topics/containers/whats-a-linux-container)

[https://dev.classmethod.jp/articles/load-balancing-types-and-algorithm/](https://dev.classmethod.jp/articles/load-balancing-types-and-algorithm/)

[https://huisam.tistory.com/entry/k8s-deployment](https://huisam.tistory.com/entry/k8s-deployment)

[https://velog.io/@djm0727/MLOps-in-K8s1.-Pod](https://velog.io/@djm0727/MLOps-in-K8s1.-Pod)

[https://malwareanalysis.tistory.com/151](https://malwareanalysis.tistory.com/151)

[https://bcho.tistory.com/1259](https://bcho.tistory.com/1259)

[https://stackshare.io/stackups/kubernetes-vs-minikube](https://stackshare.io/stackups/kubernetes-vs-minikube)