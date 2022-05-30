---
title: "PV & PVC stuck in terminating status"
date: 2022-03-23
categories: [Kubernetes]
tags: [Kubernetes, PV, PVC, Terminating]
description: PV & PVC stuck in terminating status
toc: true
published: true
---

# PV & PVC stuck in terminating status

1. PV, PVC 삭제 안될때  **--grace-period=0** 옵션으로 해보고 그래도 안되면 **--grace-period=0 --force** 으로 처리.

1. 이것도 안되면 **k edit** 로 내용 확인.
    > kubernetes.io/pv-protection    

1. patch로 삭제

    ```console
     $ k patch pvc <pvc_name> -p '{"metadata":{"finalizers":null}}'
     $ k patch pv <pv_name> -p '{"metadata":{"finalizers":null}}'
     $ k patch pod <pod_name> -p '{"metadata":{"finalizers":null}}'
    ```
