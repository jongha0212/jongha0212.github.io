---
title: "Kubernetes Install"
date: 2020-06-11
categories: [Kubernetes]
tags: [Kubernetes, Install]
description: Kubernetes Install
toc: true
---

## Install Kubernetes on Ubuntu 18.04 LTS <a name="install-kubernetes-on-ubuntu-18.04-lts"></a>

쿠버네티스는 기본적으로 **Master Node**와 **Worker Node**로 구성됩니다.  
Master Node와 Worker Node는 최소 1개씩 필요하며, 본 가이드에서 **Ubuntu 18.04 LTS 기준**으로 작성되었음 확인하시기 바랍니다.    

  * Kubernetes Master/Worker Node 정보

  | <center>구  분</center> | <center>HOSTNAME</center> | <center>Docker Version</center> | <center>Kubernetes Version</center> |
  |:---:|:---:|:---:|:---:|
  | Master Node | xxx.xxx.xxx.xxx | 19.03.11 | 1.18.3 |
  | Worker Node | xxx.xxx.xxx.xxx | 19.03.11 | 1.18.3 |


기본적으로 Kubernetes를 설치하려면 Docker가 설치되어 있어야하며, Docker 설치는 [[가이드]]({{ site.url }}{{ site.baseurl }}/docker/Docker/)를 참조하시기 바랍니다.

---

### Kubernetes **설치** <a name="kubernetes-설치"></a>

1. 신뢰할 수 있는 APT 키 추가    
    > $ sudo apt install apt-transport-https  
    > $ curl -s <https://packages.cloud.google.com/apt/doc/apt-key.gpg> | sudo apt-key add

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/apt-transport.png ){: .align-center}
1. Repository 추가
    > $ echo "deb <https://apt.kubernetes.io/> kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/add-repository.png ){: .align-center}
1. Kubernetes 설치
    > $ sudo apt-get update  
    > $ sudo apt-get install -y kubelet kubeadm kubectl
    >
    >> * 버전별 설치 방법    
    >> $ sudo apt-get install -y kubeadm={ 설치 버전 정보 } kubelet={ 설치 버전 정보 } kubectl={ 설치 버전 정보 }    
    >> $ sudo apt-get install -y kubeadm=1.18.0-00 kubelet=1.18.0-00 kubectl=1.18.0-00    
    >> $ sudo apt-get install -y kubeadm=1.17.5-00 kubelet=1.17.5-00 kubectl=1.17.5-00    

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-install.png ){: .align-center}
1. Kubernetes 버전 확인
    > $ sudo kubeadm version  
    > $ sudo kubectl --version  
    > $ sudo kubectl version  

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-version.png ){: .align-center}

1. _**패키지가 자동으로 설치, 업그레이드, 제거되지 않도록 hold 시 사용**_
    > $ sudo apt-mark hold kubelet kubeadm kubectl    
    > $ sudo apt-mark unhold kubelet kubeadm kubectl

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-hold.png ){: .align-center}

---

### Master Node 초기화 <a name="master-node-초기화"></a>

Kubernetes가 성공적으로 설치 되었을 경우, Master(**Control Plane**) Node를 초기화해야합니다.

#### swap off <a name="swap-off"></a>

Kubernetes는 Master, Worker Node 모두 swap off를 해야합니다.  

```
$ sudo swapoff -a
```

![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/swapoff.png ){: .align-center}

swap이 off로 되어 있지 않으면, **kubeadm init** 단계에서 다음과 같은 메세지가 출력됩니다.  
```
[ERROR]: running with swap on is not supported. Please disable swap
```

#### Master Node init <a name="master-node-init"></a>
```
$ sudo sysctl net.bridge.bridge-nf-call-iptables=1
$ sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=192.168.150.56
```

kubeadm를 초기화 합니다. -pod-network-cidr는 **10.244.0.0/16**으로 설정해야 합니다. Docker Version으로 인한 Error가 발생하면 kubeadm init 마지막에 '–ignore-preflight-errors=SystemVerification'를 붙여줍니다.

* Network Plugin에 따른 pod-network-cidr 값  

  | <center>Network Plugin</center> | <center>pod-network-cidr</center> |
  |:---:|:---:|
  |             Flannel             |           10.244.0.0/16           |
  |             Calico              |          192.168.0.0/16           |
  |             Cilium              |          192.167.0.0/16           |

Pod 네트워크가 호스트 네트워크와 겹치면 문제가 발생할 수 있기 때문에 일부로 호스트 네트워크로 잘 사용하지 않을 것 같은 네트워크 대역을 권장하는 것입니다.

* 초기화 결과

![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-init.png ){: .align-center}

```
[ubuntu@dev-master /home/ubuntu]$ sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=xxx.xxx.xxx.xxx
W0615 05:27:47.196217   15748 configset.go:202] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
[init] Using Kubernetes version: v1.18.3
[preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [dev-master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 xxx.xxx.xxx.xxx]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [dev-master localhost] and IPs [xxx.xxx.xxx.xxx 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [dev-master localhost] and IPs [xxx.xxx.xxx.xxx 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
W0615 05:30:07.827010   15748 manifests.go:225] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[control-plane] Creating static Pod manifest for "kube-scheduler"
W0615 05:30:07.829074   15748 manifests.go:225] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 27.504680 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.18" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node dev-master as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node dev-master as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: rongp5.si4jcn12t6ehwfuc
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join xxx.xxx.xxx.xxx:6443 --token rongp5.si4jcn12t6ehwfuc \
    --discovery-token-ca-cert-hash sha256:6af2b76e6245ceb5287c3b8e37727f3786c4292d82c72fa251874deddd2cc3b3
```

초기화 후 하단에 노드 추가하는 명령어가 있습니다.  
노드 추가할 때 위의 명령어를 사용해야하므로 복사해 두시기 바랍니다. 또한, 설치한 클러스터를 사용하려면 위의 명령어를 실행해야 합니다.  
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-config.png ){: .align-center}


해당 명령어는 root 계정이 아닌 다른 사용자 계정에서 **kubectl** 명영어를 사용하여 클러스터를 제어하기 위해 사용하는 명령어 입니다.  
기본적으로 kubernetes에서는 **/etc/kubernetes/admin.conf** 파일을 가지고 kubernetes 관리자 Role의 인증 및 인가 처리를 하며,  
위 명령어는 사용자 계정의 **$HOME/.kube/config** 디렉터리에 **admin.conf** 파일을 복사함으로써 사용자 계정이 kubectl을 사용하면서 관리자 Role의 인증 및 인가 처리를 받을 수 있도록 하는 것입니다.

여기서 말한 사용자 계정이란, Master Node의 Shell에 접속한 계정이다.더 깊게 들어가보면 admin.conf는 **클러스터에 접근할 수 있는 인증의 역할**만을 하고,  
클러스터의 리소스에 접근하여 제어하기 위한 Role에 대한 권한 허용은 해당 계정(admin.conf)에 **rolebinding을 통해서 가능**합니다.  
즉, **RBAC**(Role-based access control) 방식을 통해 인증 및 인가 시스템을 사용한다고 보면 됩니다.

RBAC는 사용자와 역할을 별개로 생성한 후 서로를 엮어서(binding) **사용자에게 역할에 대한 권한을 부여하는 방식**입니다.  
kubeadm은 기본적으로 안전한 클러스터 구성을 위하여 RBAC 사용을 권장합니다.  
만약 다른 컴퓨터에서 kubectl을 사용하여 클러스터와 통신하고 싶다면 아래와 같이 admin.conf 파일을 마스터 노드에서 복사한 후 kubectl --kubeconfig 명령어를 통해 가능합니다.   

```
$ kubectl --kubeconfig /path/admin.conf get nodes
```

#### Pod Network 추가 <a name="pod-network-추가"></a>

우선 세팅할 클러스터에서 Pod가 서로 통신할 수 있도록 Pod 네트워크 애드온을 설치해야 합니다.  
kubeadm을 통해 만들어진 클러스터는 **CNI**(Container Network Interface) 기반의 애드온이 필요합니다.

기본적으로 kubernetes에서 제공해주는 **kubenet**이라는 네트워크 플러그인이 있지만,  
매우 기본적이고 간단한 기능만 제공하는 네트워크 플러그인이기 때문에 이 자체로는 크로스 노드 네트워킹이나 네트워크 정책과 같은 고급 기능은 구현되어 있지 않습니다.  
따라서, kubeadm은 kubernetes가 기본적으로 지원해주는 네트워크 플러그인인 kubenet을 지원하지 않고, CNI 기반 네트워크만 지원한다.

클러스터에서 Pod가 서로 통신할 수 있도록 Pod 네트워크 애드온을 설치해야 합니다. Master Node를 초기화할 때에 사용하는 Pod Network에 따라 초기화 코드가 달라질수 있으며,  
본 가이드에서는 Pod Network를 **flannel**로 지정하여 초기화 하도록 하겠습니다.  
종류별 Pod Network 사용 방법 및 초기화 코드는 다음 [사이트](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network)를 참고하시기 바랍니다.
  > $ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml    

  ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-flannel.png ){: .align-center}

Master Node가 잘 세팅 되었는지 아래 명령어를 통해 확인해볼 수 있습니다.

* 클러스터 노드 정보 확인
    > $ kubectl get nodes

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-get-node.png ){: .align-center}

---

### Worker Node 추가 <a name="worker-node-추가"></a>

Mastet Node를 생성하고 출력된 메세지의 맨 하단의 아래와 같은 메세지를 확인할 수 있습니다.

```
Then you can join any number of worker nodes by running the following on each as root:

kubeadm join xxx.xxx.xxx.xxx:6443 --token rongp5.si4jcn12t6ehwfuc \
    --discovery-token-ca-cert-hash sha256:6af2b76e6245ceb5287c3b8e37727f3786c4292d82c72fa251874deddd2cc3b3
```

Worker Node에서 위의 명령어를 실행시키면 해당 Worker Node가 **Master Node에 Join**되고, 정상적으로 Join되면 다음과 같은 메세지를 확인할 수 있습니다.  

![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-join.png ){: .align-center}

Master Node에서 다음 명령어를 실행하여 Worker Node가 Join 상태를 확인할 수 있습니다.
  > $ kubectl get nodes    

  ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-join-status.png ){: .align-center}


#### Worker Node Join Argument <a name="worker-node-join-argument"></a>
* "--token"
token은 Master Node에서 기본적으로 24시간 뒤 만료됩니다.
    > $ sudo kubeadm token list

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-token-list.png ){: .align-center}

* "--discovery-token-ca-cert-hash"
Worker Node에 Control Plane의 CA를 검증하는 메커니즘을 제공합니다. CA의 SHA256 해시값을 미리 공유함으로써 Worker Node의 의도된 Control Plane에서 받은 자격 증명인지 유효성을 검사할 수 있습니다.
    > $ openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'

    ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-ca.png ){: .align-center}

#### Token 만료 시 Join 방법 (**Worker Node에서 실행**) <a name="token-만료-시-join-방법"></a>
  1. token 생성
      > $ sudo kubeadm token create    

      ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-token-create.png ){: .align-center}
  1. CA SHA256 Hash 확인
      > $ openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'

     ![]({{ site.url }}{{ site.baseurl }}/assets/images/kubernetes/install/kube-ca.png ){: .align-center}
  1. join 명령어
      > $ kubeadm join { Worker Node IP }:6443 --token { 1. Token 생성 값 } --discovery-token-ca-cert-hash sha256:{ 2. CA SHA256 값 }
      ```
      kubeadm join xxx.xxx.xxx.xxx:6443 --token 0coiv6.iizap0gnu8yuyae9 \
          --discovery-token-ca-cert-hash sha256:6af2b76e6245ceb5287c3b8e37727f3786c4292d82c72fa251874deddd2cc3b3
      ```

---

### Kubernetes **삭제** <a name="kubernetes-삭제"></a>
Kubernetes를 삭제 하려면 다음 명령어를 순차적으로 실행하시면 Kubernetes가 정상적으로 삭제됩니다.  
(* _해당 명령어는 OS에 따라 다소 다를수 있습니다._)

```
$ sudo kubeadm reset
$ sudo systemctl stop kubelet

$ sudo rm -rf /var/lib/cni/
$ sudo rm -rf /var/lib/kubelet/*
$ sudo rm -rf /run/flannel
$ sudo rm -rf /etc/cni/
$ sudo rm -rf /etc/kubernetes
$ sudo rm -rf /var/lib/etcd/

$ sudo ip link delete cni0
$ sudo ip link delete flannel.1

$ sudo apt remove -y kubelet kubectl kubeadm
```
