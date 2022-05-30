---
title: "Remove Kubernetes namespace stuck in terminating"
date: 2022-03-23
categories: [Kubernetes]
tags: [Kubernetes, Namespace, Terminating]
description: Remove Kubernetes namespace stuck in terminating
toc: true
published: true
---

# Remove Kubernetes namespace stuck in terminating

kubernetes namespace의 .spec.finalizers는 sub resourece이기 때문에 kubectl edit, kubectl update등의 cli로 처리되지 않는다. curl or postman 등으로 직접 API를 call해서 처리해야 한다.

```console
 $ kubectl proxy &
 $ NAMESPACE=<삭제하려는 namespace name>
 $ kubectl get ns ${NAMESPACE} -ojson | grep -v '"kubernetes"$' | curl -k -H "Content-Type: application/json" -XPUT --data-binary @- localhost:8001/api/v1/namespaces/$NAMESPACE/finalize
 $ pkill kubectl
```
