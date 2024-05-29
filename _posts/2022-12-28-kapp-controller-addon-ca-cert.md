---
title: "kapp Controller Add-on Ca-Cert"
date: 2022-12-28
categories: [Tanzu]
tags: [Tanzu, TKG, TAP, kapp]
description: kapp Controller Add-on Ca-Cert
toc: true
published: true
---

# kapp Controller Add-on Ca-Cert
* Tanzu Application Platform 설치 시 **package repository add** 과정에서 발생하는 인증서 오류를 해결하기 위함.

    ```
    x509: certificate signed by unknown authority
    ```

### Management Cluster의 kapp controller addon
1. management cluster에서 관리되고 있는 kapp controller secret 확인
    ```
    $ k get secret -A
    NAMESPACE       NAME                                 TYPE                                 DATA   AGE
    default         labs-hjh-kapp-controller-addon       tkg.tanzu.vmware.com/addon           1      27h
    ...
    ...
    tkg-system      tanzu-addons-manager-fetch-0         kubernetes.io/dockerconfigjson       1      30h
    tkg-system      tanzu-addons-manager-values          Opaque                               1      30h
    ```
1. *labs-hjh-kapp-conteroller-addon* 의 *data.values.yaml* 파일 내용 export
    ```
    $ k get secret -n default labs-hjh-kapp-controller-addon -o jsonpath='{.data.values\.yaml}' | base64 -d > values.yaml
    ```
1. **kappController.config.caCerts**에 custom repository ca-cert 추가
    ```
    config:
      caCerts: |
        -----BEGIN CERTIFICATE-----
        MIIF7DCCA9SgAwIBAgIUWaQBfTNs1QDYd4nhsMGNmHaAoYgwDQYJKoZIhvcNAQEN
        ...
        ...
        d8ygiLnlhX704Pr05wUhQWgSUFHwhb4BXSmh8AYuyns=
        -----END CERTIFICATE-----
    ```
1. sercet patch
    ```
    $ k patch secret/labs-hjh-kapp-controller-addon -n default -p "{\"data\":{\"values.yaml\":\"$(base64 -w 0 < values.yaml)\"}}" --type=merge
    secret/labs-hjh-kapp-controller-addon patched
    ```
1. patch 확인
    ```
    $ k get secret -n default labs-hjh-kapp-controller-addon -o jsonpath='{.data.values\.yaml}' | base64 -d
    ```

### Tanzu Application Platform에 custom repository 추가
1. TAP를 설치하기 위한 *package repository add*
    ```
    $ tanzu package repository add tanzu-tap-repository   --url $IMGPKG_REGISTRY_HOSTNAME/tap/tap-packages:$TAP_VERSION   --namespace tap-install

    Waiting for package repository to be updated

    7:03:34PM: Waiting for package repository reconciliation for 'tanzu-tap-repository'
    7:04:52PM: Fetch started (4s ago)
    7:04:52PM: Fetching (4s ago)
           | apiVersion: vendir.k14s.io/v1alpha1
           | directories:
           | - contents:
           |   - imgpkgBundle:
           |       image: harbor.hjh.labs.ds/tap/tap-packages@sha256:db1362fa1a63d15a73729ba970b21f596299c5754029786c75b36ce38d29fcc7
           |       tag: 1.5.0
           |     path: .
           |   path: "0"
           | kind: LockConfig
           |
    7:04:52PM: Fetch succeeded (4s ago)
    7:04:53PM: Template succeeded (3s ago)
    7:04:53PM: Deploy started (3s ago)
    7:04:57PM: Deploying
           | Target cluster 'https://100.64.0.1:443'
           | Changes
           | Namespace  Name  Kind  Age  Op  Op st.  Wait to  Rs  Ri
           | Op:      0 create, 0 delete, 0 update, 0 noop, 0 exists
           | Wait to: 0 reconcile, 0 delete, 0 noop
           | Succeeded
    7:04:57PM: Deploy succeeded
    ```
1. repository 확인
    ```
    $ tanzu package repository list -A
     NAMESPACE                  NAME                  SOURCE                                                                  STATUS               
     tanzu-package-repo-global  tanzu-standard        (imgpkg) harbor.labs.ds/tkg/packages/standard/repo:v2.1.1               Reconcile succeeded  
     tap-install                tanzu-tap-repository  (imgpkg) harbor.hjh.labs.ds/tap/tap-packages:1.5.0                      Reconcile succeeded  
     tkg-system                 tanzu-core            (imgpkg) harbor.labs.ds/tkg/packages/core/repo:v1.24.10_vmware.1-tkg.2  Reconcile succeeded  
    ```
