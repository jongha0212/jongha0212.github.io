---
title: "TLS Certificate Expire Date"
date: 2022-05-30
categories: [Kubernetes]
tags: [Kubernetes, Secret, TLS, Certificate]
description: TLS Certificate Expire Date
toc: true
published: true
---

# TLS Certificate Expire Date

1. Kubernetes Secret(kubernetes.io/tls)의 인증서 만료 날짜 확인

    ```console
     $ k get secrets -n <namespace> <secret_name> -o "jsonpath={.data['tls\.crt']}" | base64 -d | openssl x509 -enddate -noout
    notAfter=May 26 23:59:59 2023 GMT
    ```

1. Subject CN 확인

    ```console
     $ kubectl get secret -n <namespace> <secret_name> -o json | jq -r '.data."tls.crt"' | base64 -d | openssl x509 -noout -text | grep "Subject: CN = " | sed -E 's/\s+Subject: CN = ([^ ]*)/\1/g'
    www.example.com
    ```
