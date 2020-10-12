---
title: 將健康情況探查新增至 AKS pod
description: 本文提供有關如何使用應用程式閘道，將健康情況探查 (就緒和/或活動) 新增至 AKS pod 的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8c8b8b0090877db7abc8fae0e44f928e8b10dcf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807999"
---
# <a name="add-health-probes-to-your-service"></a>將健康情況探查新增至您的服務
根據預設，輸入控制器會為公開的 pod 布建 HTTP GET 探查。
您可以藉由將[就緒或活動探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)新增至您的規格，來自訂探查屬性 `deployment` / `pod` 。

## <a name="with-readinessprobe-or-livenessprobe"></a>使用 `readinessProbe` 或 `livenessProbe`
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
* [HttpGet 動作](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe``livenessProbe`使用設定時，支援和 `httpGet` 。
> * 目前不支援在 pod 上公開的埠上進行探查。
> * `HttpHeaders``InitialDelaySeconds` `SuccessThreshold` 不支援、和。

##  <a name="without-readinessprobe-or-livenessprobe"></a>不含 `readinessProbe` 或 `livenessProbe`
如果未提供上述探查，則輸入控制器會假設在 `Path` 指定 for `backend-path-prefix` annotation 或在 `path` 服務定義中指定的可連線到服務 `ingress` 。

## <a name="default-values-for-health-probe"></a>健康情況探查的預設值
對於準備/活動探查無法推斷的任何屬性，會設定預設值。

| 應用程式閘道探查屬性 | 預設值 |
|-|-|
| `Path` | / |
| `Host` | 本機主機 |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |