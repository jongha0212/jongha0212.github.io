---
title: "KubeSphere"
date: 2020-06-11
categories: [Kubernetes]
tags: [Kubernetes, KubeSphere]
description: KubeSphere 3.0.0
toc: true
---

## Kubesphere

KubeSphere는 Kubernetes를 커널로 사용하여 클라우드 **네이티브 애플리케이션** 을 **관리** 하는 **분산 운영 체제**로,    
타사 애플리케이션의 원활한 통합을 위한 플러그 앤 플레이 아키텍처를 제공합니다.    

또한 KubeSphere는 풀 스택 자동화 IT 운영과 간소화된 DevOps 워크 플로를 갖춘 멀티 테넌트 엔터프라이즈 급 컨테이너 플랫폼을 나타냅니다.    
개발자 친화적 인 웹 UI를 제공하여 기업이 보다 강력하고 기능이 풍부한 플랫폼을 구축 할 수 있도록 지원합니다.    
Kubernetes 리소스 관리, DevOps (CI/CD), 애플리케이션 수명주기 관리, 모니터링, 로깅, 서비스 메시, 멀티 테넌시, 경고 및 알림, 감사, 스토리지 및 네트워킹과 같은    
엔터프라이즈 Kubernetes 전략에 필요한 가장 일반적인 기능을 자랑합니다.
  * 자동 확장, 액세스 제어, GPU 지원, 다중 클러스터 배포 및 관리, 네트워크 정책, 레지스트리 관리, 보안 관리    

KubeSphere는 통합 뷰를 제공하는 동시에 Kubernetes 주변 의 광범위한 에코 시스템 도구를 통합하여 일관된 사용자 경험을 제공하여 복잡성을 줄입니다.    
동시에 업스트림 Kubernetes에서 아직 사용할 수 없는 새로운 기능을 제공하여 스토리지, 네트워크, 보안 및 유용성을 포함한 Kubernetes의 문제점을 완화합니다.    
KubeSphere를 사용하면 개발자와 DevOps팀이 통합 콘솔에서 가장 좋아하는 도구를 사용할 수 있을뿐만 아니라,    
가장 중요한 것은 이러한 기능이 플러그 가능하고 선택 사항이기 때문에 플랫폼과 유연하게 결합된다는 것입니다.    

### Run Kubesphere Everywhere

KubeSphere는 경량 플랫폼으로서 kubernetes 자체를 전혀 변경하지 않기 때문에 다양한 클라우드 에코 시스템에 더욱 친숙해졌습니다.    
즉, KubeSphere는 모든 인프라의 기존 버전 호환 Kubernetes 클러스터에 배포 할 수 있습니다.    
가상 머신, 베어 메탈, 온 프레미스, 퍼블릭 클라우드 및 하이브리드 클라우드를 포함합니다.     
KubeSphere 사용자는 Alibaba Cloud, AWS, QingCloud, Tencent Cloud, Huawei Cloud 및 Rancher와 같은     
클라우드 및 컨테이너 플랫폼에 KubeSphere를 설치하고 주요 Kubernetes 배포를 사용하여 생성 된 기존 Kubernetes 클러스터를 가져오고 관리 할 수 ​​있습니다.     
KubeSphere를 기존 Kubernetes 플랫폼에 원활하게 통합하면 현재 리소스 또는 자산을 수정하지 않고도 사용자의 비즈니스에 영향을 미치지 않습니다.    

KubeSphere는 하부 인프라에서 사용자를 선별하고 기업이 다양한 인프라 유형에서 기존 및 컨테이너화 된 앱을 현대화, 마이그레이션, 배포 및 관리 할 수 ​​있도록 지원합니다.    
이것이 바로 KubeSphere가 개발자와 운영팀이 애플리케이션 개발에 집중하고 DevOps 자동화 워크플로 및 프로세스를 가속화하는 방법입니다.    

![]({{ sit.url }}{{ site.baseurl }}/assets/images/kubernetes/kubesphere/ecosystem.png){: .align-center}

### Kubesphere 사용자 유형
다중 테넌트를 통해 여러 팀이 컨테이너화 된 애플리케이션을 클라우드에서 엣지까지 안전하게 배포하고 유지할 수 있습니다.     
친숙한 콘솔에서 몇 번의 클릭으로 코드를 배포 할 수 있으며 특정 벤더의 에코시스템에 종속적이지 않으며 효율적이고 유연한 네트워크 솔루션으로 Kubernetes 클러스터를 설치 및 유지 관리 할 수 ​​있습니다.

* 인프라팀

  > 클라우드에서 데이터센터로 자동 설치, 확장 및 업그레이드    

  * 리소스 활용도 향상 및 내부 인프라 비용 절감
  * 보안 강화 기능을 제공하고 여러 스토리지 및 네트워크 솔루션 지원
  * 신뢰할 수 있고 인증 된 Kubernetes 플랫폼 및 배포 제공
  * 멀티 클라우드 및 멀티 클러스터 Kubernetes 관리 지원, 벤더 잠금 방지 (coming soon)    

* 개발자

  > 복잡한 YAML에서 벗어나 개발자가 비즈니스에 집중    

  * 원활한 사용자 경험을 만들고 클라우드 네이티브 스택의 학습 효과 상승
  * 모든 애플리케이션 환경에 맞는 툴킷 및 배포 자동화 제공
  * 소스 코드에서 재현 가능한 이미지를 작성하여 개발 효율성을 높이기 위한 기본 툴킷 제공
  * 출시 기간을 단축하는 애플리케이션 수명주기 관리 지원    

* 운영팀

  > One-Stop 엔터프라이즈급 DevOps 프레임워크 구축    

  * 인프라에서 애플리케이션까지 중앙 집중식 로그 수집, 모니터링, 경고, 이벤트 및 감사 로그 제공
  * 지속적인 배포, 테스트, 릴리스, 업그레이드 및 확장의 간소화
  * 클라우드 네이티브 앱을위한 Kubernetes 내에서 향상된 추적, 라우팅 및 최적화 된 커뮤니케이션
  * 사용하기 쉬운 웹 터미널 및 그래픽 패널로 다른 사용자의 요구를 충족    

* 사용자

  > Kubernetes에서 앱을 실행하고 쉽게 사용    

  * 한 번의 클릭으로 기본 인프라에 앱 배포 및 업그레이드
  * 주문형 컨테이너 리소스 및 HPA를 제공하여 응용 프로그램의 안정성과 유연성 강화
  * 몇 초 만에 Helm 리포지토리를 가져와 시각적으로 응용 프로그램을 배포 및 업그레이드
  * 응용 프로그램 metering, billing를 포함하여 응용 프로그램 저장소에서 작업 지원 (coming soon)

### Kubesphere Cloud Native Architecture

![]({{ sit.url }}{{ site.baseurl }}/assets/images/kubernetes/kubesphere/kube_archi.png){: .align-center}

### Kubesphere 3.0 새로운 기능


* Cluster Management
  * 여러 Kubernetes 클러스터 관리 지원
  * 여러 클러스터에서 Federated Deployment 및 Federated StatefulSet 지원

* Observability
  * KubeSphere 콘솔에서 타사 애플리케이션 메트릭에 대한 사용자 지정 모니터링 지원
  * 이벤트 보관, 검색 및 경고를 포함한 Kubernetes 및 KubeSphere 지원 추가
  * Kubernetes 이벤트 보관, kube-events 기반 검색 및 경고를 포함한 Kubernetes 이벤트 관리 지원 추가
  * 테넌트 제어를 추가하고 Kubernetes 이벤트 검색을 지원
  * Elasticsearch, Kafka 또는 Fluentd에 로그 및 Kubernetes 이벤트 보관 지원
  * 알림 관리자의 다중 테넌트 알림 지원 추가
  * Alertmanager v0.21.0 지원

* DevOps
  * DevOps 프레임 워크를 리팩터링하고 CRD를 사용하여 DevOps 리소스를 관리

* App Store
  * Helm V3 지원
  * 여러 클러스터에 애플리케이션 템플릿 배포 지원
  * 지원 애플리케이션 템플릿 업그레이드
  * 사용자는 저장소 동기화 중에 발생하는 이벤트를 볼 수 있음

* Network
  * 컨트롤러를 추가하여 사용자 지정 프로젝트 네트워크 정책을 관리하여 프로젝트 네트워크 격리 지원
  * 작업 공간 네트워크 격리 지원
  * 기본 Kubernetes 네트워크 정책 추가, 보기, 수정 및 삭제 지원

* Service Mesh
  * Jagger ES 인덱서 클리닝 지원

* Storage
  * 볼륨 스냅 샷 관리 지원
  * 스토리지 용량 관리 지원
  * 볼륨 모니터링 지원

* Security
  * LDAP 및 OAuth 로그인 지원
  * 사용자 지정 작업 영역 역할 지원
  * 사용자 지정 DevOps 프로젝트 역할 지원
  * 여러 클러스터에서 액세스 제어 지원
  * 포드 보안 컨텍스트 지원

* Globalization
  * 스페인어 및 중국어 번체를 포함하여 웹 콘솔에서 새 언어에 대한 지원 추가

* User Experience
  * Toolbox에서 내역 기록보기에 대한 지원을 추가
  * 사용자는 최근에 방문한 Clusters / Workspaces / Projects / DevOps 프로젝트를 다시 방문 할 수 있으며 바로 가기 키를 통해 시작할 수도 있음

### Open Source

오픈 소스 모델을 채택함에 따라 개발은 개방적인 방식으로 진행되고 있으며 KubeSphere 커뮤니티가 주도합니다.    
KubeSphere는 100 % 오픈 소스 이며 모든 소스 코드, 문서 및 토론을 찾을 수있는 GitHub 에서 사용할 수 있습니다.    
개발, 테스트 및 생산 환경에서 널리 설치 및 사용되었으며 KubeSphere에서 많은 서비스가 원활하게 실행되고 있습니다.    

#### Kubesphere Version별 Dashboard

* Kubesphere 3.0
![]({{ sit.url }}{{ site.baseurl }}/assets/images/kubernetes/kubesphere/kube_v3.png){: .align-center}

  * Kubesphere 3.0 Dashboard
  ![]({{ sit.url }}{{ site.baseurl }}/assets/images/kubernetes/kubesphere/kube_v3_1.png)

* Kubesphere 2.1
![]({{ sit.url }}{{ site.baseurl }}/assets/images/kubernetes/kubesphere/kubesphere.png){: .align-center}

### Roadmap

![]({{ sit.url }}{{ site.baseurl }}/assets/images/kubernetes/kubesphere/roadmap.png){: .align-center}

### Storage Classe 생성

![]({{ sit.url }}{{ site.baseurl }}/assets/images/kubernetes/kubesphere/storageclass.png){: .align-center}

### Persistence Volume 생성

![]({{ sit.url }}{{ site.baseurl }}/assets/images/kubernetes/kubesphere/create_pv.png){: .align-center}

### Deploying KubeSphere

```
kubectl apply -f https://raw.githubusercontent.com/kubesphere/ks-installer/v3.0.0/deploy/kubesphere-installer.yaml
kubectl apply -f https://raw.githubusercontent.com/kubesphere/ks-installer/v3.0.0/deploy/cluster-configuration.yaml
```

### Inspect the logs of installation

```
kubectl logs -n kubesphere-system $(kubectl get pod -n kubesphere-system -l app=ks-install -o jsonpath='{.items[0].metadata.name}') -f
```

  * Kubesphere Installed    
    ![]({{ sit.url }}{{ site.baseurl }}/assets/images/kubernetes/kubesphere/installed.png)

### etcd 인증서 생성

```
sudo kubectl -n kubesphere-monitoring-system create secret generic kube-etcd-client-certs  \
--from-file=etcd-client-ca.crt=/etc/kubernetes/pki/etcd/ca.crt  \
--from-file=etcd-client.crt=/etc/kubernetes/pki/etcd/healthcheck-client.crt  \
--from-file=etcd-client.key=/etc/kubernetes/pki/etcd/healthcheck-client.key
```

### ks-installer의 ClusterConfiguration 설정 변경

```
kubectl edit cc ks-installer -n kubesphere-system
```
