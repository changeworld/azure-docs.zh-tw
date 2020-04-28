---
title: 將健康情況探查新增至您的 AKS pod
description: 本文提供有關如何使用應用程式閘道，將健康情況探查（準備就緒和/或活動）新增至 AKS pod 的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795585"
---
# <a name="add-health-probes-to-your-service"></a>將健康情況探查新增至您的服務
根據預設，輸入控制器會針對公開的 pod 布建 HTTP GET 探查。
您可以藉由將[就緒或活動探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)新增至您`deployment` / `pod`的規格來自訂探查屬性。

## <a name="with-readinessprobe-or-livenessprobe"></a>使用`readinessProbe`或`livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API 參考：
* [容器探查](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet 動作](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`使用`livenessProbe` `httpGet`設定時，支援和。
> * 目前不支援在 pod 上公開的埠上進行探查。
> * `HttpHeaders`不`InitialDelaySeconds`支援`SuccessThreshold` 、、。

##  <a name="without-readinessprobe-or-livenessprobe"></a>不`readinessProbe`含或`livenessProbe`
如果未提供上述探查`Path` ，則輸入`backend-path-prefix`控制器會假設在針對注釋指定的，或在服務的`path` `ingress`定義中指定的可連線到服務。

## <a name="default-values-for-health-probe"></a>健全狀況探查的預設值
若為任何無法由準備/活動探查推斷的屬性，則會設定預設值。

| 應用程式閘道探查屬性 | 預設值 |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |