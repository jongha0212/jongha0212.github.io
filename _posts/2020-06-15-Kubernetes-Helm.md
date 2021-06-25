---
title: "Helm"
date: 2020-06-11
categories: [Kubernetes]
tags: [Kubernetes, Helm, Kubesphere]
description: Helm v2.x
toc: true
---

# Helm <a name="helm"></a>

![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm_logo.png){: .align-center}

Helm은 Docker가 나오면서 Container 혁신이 이루어졌습니다.    
그리고 이 Container를 쉽게 빠르게 배포 & 확장하고 관리해주는 Container Orchestration인 Kubernetes가 나오게 됩니다.    
이런 Kubernetes를 Package 형태로 관리해주는 것이 바로 [**Helm**](https://helm.sh/ "Helm 공식 사이트")입니다.

Helm은 Kubernetes Package Managing Tool입니다. Pyton의 pip, nodes.js의 npm과 비슷한 형태로 Kubernetes 패키지 배포를 가능하게 하는 Tool이라고 보시면 됩니다.    
Helm을 사용하시다보면 **Chart** & **Helm Chart**라는 용어를 많이 접하시게 됩니다.    
chart라고 부르는 package format을 사용하는데 chart는 Kubernetes Resource를 Describe하는 파일들의 집합입니다.

---

## Helm Architecture <a name="helm-architecture"></a>

### Helm 목적 <a name="helm-목적"></a>
  * **스크래치**(scratch)부터 새로운 차트 생성
  * **차트 아카이브**(tgz) 파일로 차트 패키징
  * 차트가 저장되는 곳인 차트 **리포지터리와 상호작용**
  * **Kubernetes Cluster에 차트** 인스톨 및 언인스톨
  * Helm으로 설치된 차트들의 **릴리스 주기 관리**

![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm-architecture.png){: .align-center}

### Helm 3가지 개념 <a name="helm-3가지-개념"></a>
  * **Chart**는 쿠버네티스 애플리케이션의 인스턴스를 생성하는 데에 필요한 정보의 패키지입니다.  
  * **Configrations**은 릴리스 가능한 객체를 생성하기 위해 패키징된 차트로 병합될 수 있는 설정 정보를 가집니다.  
  * **Release**는 _차트_ 의 구동중 인스턴스이며, 특정 _설정_ 이 결합되어 있습니다.    

### Components <a name="componets"></a>

Helm은 실행프로그램이며, 다음 2가지 부분으로 나누어 구현되어 있습니다.

1. **Helm Client**는 엔드유저용 command-line 클라이언트입니다. 이 클라이언트는 다음과 같은 역할을 합니다.
    * **Local chart** development
    * Managing **repositories**
    * Managing **releases**
    * **Interfacing** with the Helm library
      * 설치할 차트를 전달
      * 기존 릴리스에 대한 업그레이드 또는 언인스톨 요청
1. **Helm Library**는 모든 Helm 동작 수행에 대한 로직을 제공합니다. 쿠버네티스 API 서버에 대한 인터페이스 역할을 하며 다음의 기능들을 제공합니다.
    * 릴리스를 빌드하기 위해 차트와 설정 결합
    * 쿠버네티스에 차트 설치, 후속 릴리스 객체 제공
    * 쿠버네티스와 상호작용하여 업그레이드 및 언인스톨 수행

단독형 Helm 라이브러리에는 Helm 로직이 캡슐화되어 있어 다른 클라이언트에서 효과적으로 활용할 수 있습니다.

Helm Client & Library는 **Go 언어**로 작성되었으며,    
라이브러리는 쿠버네티스와 연동하기 위해 쿠버네티스 클라이언트 라이브러리를 사용합니다.    
현재, 라이브러리는 REST+JSON을 사용하며, 쿠버네티스 내부에 위치한 시크릿(secret) 안에 정보를 저장합니다. 따라서 별도의 데이터베이스가 필요하지 않습니다.    

---

## Helm Chart <a name="helm-chart"></a>

Helm Chart는 특정 Directory 하위에 모여있는 파일들을 chart라고 부르는데,    
chart의 형태는 다음과 같은 구조를 되어 있으며 버전 정보가 붙은 tgz 형태의 압축 패키징 됩니다.    

**gitlab-4.0.2.tgz** 파일은 **4.0.2** 버전의 **gitlab chart**이며 Chart에 대한 더 자세한 사항은 [**Helm Chart Template Guide**](https://helm.sh/docs/chart_template_guide/ "Chart Template Guide")를 참고하시면 됩니다.

  * Gitlab Chart Sample    
    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm-chart-sample.png)

Chart Name은 Directory Name과 동일하며 Directory 안에 Chart 파일들이 있으며, 각 파일에 대한 설명은 다음과 같습니다.

  | <center>대상</center> | <left>설명</left> |
  |:---:|:---|
  | Chart.yaml | 해당 Helm Chart에 대한 정보 |
  | values.yaml | 해당 Helm Chart에서 사용하는 각종 값들에 대한 정의 |
  | charts | 의존하느 Chart에 대한 정보 |
  | templates | Kubernetes를 정의하는 manifest file이 정의되어 있는 디렉토리 |
  | README.md | 사용자가 읽을 수 있는 markdown 형태의 README 파일 |
  | repository | Chart들이 공유되는 공간. (DockerHub 개념) |
  | release | Kubernetes 환경에서 동작하는 서비스들은 release 버전이 존재함 |

Chart / Repository / Release 관계를 정리하면, Helm Chart Repository에서 Chart를 찾을 수 없으며 Helm Chart는 Kubernetes를 설명하는 파일 목록입니다.    
그리고 설치할 때마다 Release  버전이 생성됩니다.     

---

## Helm Install <a name="helm-install"></a>

Helm은 **Client** / **Server(tiller)** 두가지 파트로 나눠져 있습니다.    
**Client**는 end user를 위한 command-line client이며, Local Chart 개발이나 Repository Managing, Server(tiller)에 chart 설치 요청 등 주로 Chart를 관리합니다.    
**tiller**라고도 부르는 **Server**는 in-cluster 서버로 Chart의 배포, 릴리즈를 관리합니다.    

### Helm Client <a name="helm-client"></a>

Helm Client는 [**Github**](https://github.com/helm/helm/releases "Helm Download")에서 플랫폼에 해당하는 버전을 다운로드하여 압축 해제 후 사용이 가능합니다.  
(* **Kubesphere**를 사용하시려면 다음을 참고하시기 바랍니다. **Helm version** >= **2.10.0** and < **3.0**，see Install and Configure Helm in Kubernetes; KubeSphere 3.0 will support Helm 3.0)
본 가이드에서는 Kubesphere 사용을 위해 **Helm v2.16.8** 으로 설치하도록 하겠습니다.  아울러 Helm **v2.0**과 **v3.0**은 [차이점](#how-to-migrate-from-helm-V2-to-helm-v3)을 참고하시기 바랍니다.   

1. Helm Download    
    **압축 파일 다운로드 -> 압축 해제 -> 파일 복사 -> 소유권 변경**    
    > $ wget https://get.helm.sh/helm-v2.16.8-linux-amd64.tar.gz    
    > $ tar xvf helm-v2.16.8-linux-amd64.tar.gz    
    > $ sudo cp linux-amd64/tiller /usr/local/bin    
    > $ sudo cp linux-amd64/helm /usr/local/bin    
    > $ sudo chown root:ubuntu /usr/local/bin/tiller    
    > $ sudo chown root:ubuntu /usr/local/bin/helm    

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm-install.png)
1. Helm 설치 확인    
    설치가 잘 되었는지 다음 명령어로 확인하실 수 있습니다.    
    > $ helm version    

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm-version.png)

### Helm Server Tiller <a name="helm-server-tiller"></a>
tiller를 설치하기 위해서 서비스 계정을 생성하고, cluster-admin Role을 생성합니다. CLI로 생성하거나 yaml을 활용하여 생성해도 됩니다.    

1. ServiceAccount & ClusterRole 생성
    * yaml 활용
      ```
      $ vi rbac-tiller.yaml
      ---
      apiVersion: v1
      kind: ServiceAccount
      metadata:
        name: tiller
        namespace: kube-system
      ---
      apiVersion: rbac.authorization.k8s.io/v1
      kind: ClusterRoleBinding
      metadata:
        name: tiller
      roleRef:
        apiGroup: rbac.authorization.k8s.io
        kind: ClusterRole
        name: cluster-admin
      subjects:
        - kind: ServiceAccount
          name: tiller
          namespace: kube-system
      ```

      ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm-tiller-yaml.png)
1. ServiceAccount & ClusterRole 적용
    * CLI 활용
      > $ kubectl -n kube-system create sa tiller    
      > $ kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller    

      ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm-tiller-cli-create.png)

    * yaml 활용
      > $ kubectl apply -f ./rbac-tiller.yaml    

      ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm-tiller-yaml-create.png)
1. tiller 설치
    * CLI 활용
      > $ helm init --service-account tiller    

      ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm-tiller-init.png)

1. helm repository update
    * CLI 활용
      > $ helm repo update

      ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/heml/helm-repo-update.png)

---

## How to Migrate from Helm V2 to Helm V3 <a name="how-to-migrate-from-helm-V2-to-helm-v3"></a>

Helm의 V3의 자세한 사항은 [공식 사이트](https://v3.helm.sh/docs/faq/#changes-since-helm-2 "Helm 공식 사이트")를 참조하시기 바랍니다.

### Tiller 삭제 <a name="tiller-삭제"></a>

* 클라이언트/서버 환경에서 클라이언트/라이브러리 아키텍처로 대체합니다. (helm 바이너리만 해당)
* 보안은 이제 사용자 단위로 합니다. (Kubernetes 사용자 클러스터 보안으로 위임 됨)
* 릴리스는 이제 in-cluster secrets으로 저장되고 릴리스 객체 메타 데이터가 변경되었습니다.
* 릴리스는 더 이상 Tiller 네임 스페이스가 아닌 릴리스 **네임 스페이스를 기준으로 지속** 됩니다.

### Chart 저장소 업데이트 <a name="chart-저장소-업데이트"></a>

* **helm search** 는 로컬 리포지토리 검색과 [Helm Hub](https://hub.helm.sh/ "Helm Hub")에 대한 검색 쿼리를 모두 지원합니다.

### 다음 사양 변경으로 인해 차트 apiVersion이 **v2**로 변경 <a name="chart-apiversion"></a>

* 동적으로 연결된 차트 종속성은 Chart.yaml로 이동 (requirements.yaml 제거. 요구 사항은 –> 종속성으로 변경)
* 라이브러리 차트 (Helper / 공통 차트)를 동적으로 연결된 차트 종속성으로 추가 할 수 있습니다.
* 차트는 application 또는 library type을 가지며 차트를 정의하는 메타 데이터 필드에 정의합니다. 기본적으로 응용 프로그램이므로 렌더링 및 설치가 가능합니다.
* Helm 2 차트 (apiVersion = v1)는 여전히 설치 가능합니다.

### XDG 디렉토리 사양이 추가되었습니다. <a name="xdg-directory"></a>

* Helm home은 삭제되었습니다. Configuration저장을 위한 XDG 디렉토리 스펙은 교체되었습니다.
* 더 이상 Helm을 초기화 할 필요가 없습니다.
* helm init 과 helm home 은 제거되었습니다.

### 추가 변경 사항 <a name="추가-변경-사항"></a>

* Helm 설치 / 설정이 단순화 되었습니다
    * Helm 클라이언트만 설치 (Tiller는 없음)
    * 그대로 실행 패러다임(Run-as-is paradigm)
* local 또는 stable 리포지토리는 기본적으로 설정되어 있지 않습니다.
* **crd-install** hook은 제거되었으며 차트 안의 crds 디렉토리는 차트 렌더링 전에 디렉토리에 정의 된 모든 CRD가 설치되도록 교체되었습니다.
* test-failure hook 어노테이션 값은 제거되었으며, test-success는 더 이상 사용되지 않습니다. 대신 test를 사용합니다.
* removed/replaced/added 명령
* delete –> uninstall : 기본적으로 모든 릴리스 기록을 제거합니다 (이전에는 --purge 필요)
* fetch –> pull
* home (removed)
* init (removed)
* install : 릴리스 이름 또는 **--generate-name** 인자가 필요합니다.
* inspect –> show
* reset (removed)
* serve (removed)
* template : -x / --execute 인자의 이름이 -s / --show-only로 대체
* upgrade : 릴리스 당 저장되는 최대 revision 수를 제한하는 --history-max 인자가 추가되었습니다 (무제한은 0).
* Helm 3 Go 라이브러리는 많은 변화를 겪었으며 Helm 2 라이브러리와 호환되지 않습니다.
* 릴리스 바이너리가 **get.helm.sh** 로 호스팅됩니다.

### Helm v2를 Helm v3으로 마이그레이션 <a name="helm-v2를-helm-v3으로-마이그레이션"></a>

* 이 사용 사례는 Helm v3이 기존 Helm v2 릴리스를 관리하도록 하려는 경우에 적용됩니다.
* Helm v2 클라이언트는 다음과 같습니다.
    * 1 ~ 다수의 Kubernetes 클러스터를 관리 할 수 ​​있습니다.
    * 클러스터의 1 대 다수의 Tiller 인스턴스에 연결할 수 있습니다.
* 즉, 릴리스가 Tiller 및 해당 네임 스페이스에 의해 클러스터에 배치 될 때, 마이그레이션 할 때 임을 인식해야 합니다. 따라서 Helm v2 클라이언트 인스턴스가 관리하는 각 클러스터 및 각 Tiller 인스턴스에 대한 마이그레이션을 알고 있어야합니다.

* 권장되는 데이터 마이그레이션 경로는 다음과 같습니다.
    * v2 데이터 백업
    * Helm v2 configuration 마이그레이션
    * Helm v2 릴리스 마이그레이션
    * Helm v3이 모든 Helm v2 데이터 (Hem v2 클라이언트 인스턴스의 모든 클러스터 및 Tiller 인스턴스에 대해)를 예상대로 관리한 경우 Helm v2 데이터를 정리(clean up)하세요.
* Helm v3의 2to3 플러그인으로 마이그레이션 프로세스가 자동화됩니다.    

---

[참고1](https://kycfeel.github.io/2019/12/25/Helm-v2-%EC%97%90%EC%84%9C-v3-%EB%A1%9C-%EB%A7%88%EC%9D%B4%EA%B7%B8%EB%A0%88%EC%9D%B4%EC%85%98-%ED%95%98%EA%B8%B0/)    

---
