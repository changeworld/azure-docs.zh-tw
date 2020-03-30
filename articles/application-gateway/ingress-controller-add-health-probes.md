---
title: 將健康探頭添加到 AKS 吊艙
description: 本文提供有關如何使用應用程式閘道向 AKS pod 添加運行狀況探測（就緒和/或活動）的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795585"
---
# <a name="add-health-probes-to-your-service"></a>將運行狀況探測添加到服務
預設情況下，入口控制器將為暴露的窗格預配 HTTP GET 探測。
可以通過向`pod`規範添加[就緒或動態探測](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)`deployment`/來自訂探測器屬性。

## <a name="with-readinessprobe-or-livenessprobe"></a>與`readinessProbe`或`livenessProbe`
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

庫伯內斯 API 參考：
* [容器探頭](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet 操作](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`並且`livenessProbe`在配置 時`httpGet`受支援。
> * 當前不支援在波子上暴露的埠以外的埠上進行探測。
> * `HttpHeaders`不支援`InitialDelaySeconds` `SuccessThreshold` ，不支援 。

##  <a name="without-readinessprobe-or-livenessprobe"></a>沒有`readinessProbe`或`livenessProbe`
如果未提供上述探測，則入口控制器假定服務可`Path`根據指定的`backend-path-prefix`注釋或服務`path``ingress`的定義中指定進行。

## <a name="default-values-for-health-probe"></a>運行狀況探測的預設值
對於無法由就緒/活動度探測推斷的任何屬性，將設置預設值。

| 應用程式閘道探測屬性 | 預設值 |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |