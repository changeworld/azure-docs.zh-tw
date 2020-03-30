---
title: 使用應用程式閘道通過 HTTP 或 HTTPS 公開 AKS 服務
description: 本文提供有關如何使用應用程式閘道通過 HTTP 或 HTTPS 公開 AKS 服務的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795579"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>使用應用程式閘道通過 HTTP 或 HTTPS 公開 AKS 服務 

這些教程有助於說明[Kubernetes 入口資源](https://kubernetes.io/docs/concepts/services-networking/ingress/)通過 HTTP 或 HTTPS 通過[Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/)公開 Kubernetes 服務的示例。

## <a name="prerequisites"></a>Prerequisites

- 已`ingress-azure`安裝的掌舵圖。
  - [**嶄新部署**](ingress-controller-install-new.md)：如果您從頭開始，請參閱這些安裝說明，其中概述了在 AKS 群集上部署具有應用程式閘道的 AKS 群集和安裝應用程式閘道入口控制器的步驟。
  - [**Brownfield 部署**](ingress-controller-install-existing.md)：如果您有現有的 AKS 群集和應用程式閘道，請參閱這些說明，在 AKS 群集上安裝應用程式閘道入口控制器。
- 如果要在此應用程式上使用 HTTPS，則需要 x509 證書及其私密金鑰。

## <a name="deploy-guestbook-application"></a>部署`guestbook`應用程式

留言簿應用程式是一個規範的 Kubernetes 應用程式，由 Web UI 前端、後端和 Redis 資料庫組成。 預設情況下，`guestbook`通過埠`frontend``80`上的名稱的服務公開其應用程式。 如果沒有庫伯內斯入口資源，則無法訪問從 AKS 群集外部訪問該服務。 我們將使用應用程式並設置入口資源通過 HTTP 和 HTTPS 訪問應用程式。

按照以下說明部署留言簿應用程式。

1. 從這裡`guestbook-all-in-one.yaml`下載[here](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. 通過`guestbook-all-in-one.yaml`運行部署到 AKS 群集

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

現在，`guestbook`應用程式已部署。

## <a name="expose-services-over-http"></a>通過 HTTP 公開服務

為了公開留言簿應用程式，我們將使用以下入口資源：

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

此入口將公開`frontend``guestbook-all-in-one`部署的服務作為應用程式閘道的預設後端。

將上述入口資源另存為`ing-guestbook.yaml`。

1. 按`ing-guestbook.yaml`運行進行部署：

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. 檢查入口控制器的日誌，瞭解部署狀態。

現在應用程式`guestbook`應該可用。 您可以通過訪問應用程式閘道的公共位址來檢查此情況。

## <a name="expose-services-over-https"></a>通過 HTTPS 公開服務

### <a name="without-specified-hostname"></a>無指定主機名稱

如果不指定主機名稱，留言簿服務將在指向應用程式閘道的所有主機名稱上可用。

1. 在部署入口之前，您需要創建一個 kubernets 機密來承載證書和私密金鑰。 您可以通過運行

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. 定義以下入口。 在入口中，在`secretName`節中指定機密的名稱。

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > 在上面`<guestbook-secret-name>`的入口資源中替換為您的機密名稱。 將上述入口資源存儲在檔案名`ing-guestbook-tls.yaml`中。

1. 通過運行部署 ing-來賓簿-tls.yaml

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. 檢查入口控制器的日誌，瞭解部署狀態。

現在該應用程式`guestbook`將在 HTTP 和 HTTPS 上同時提供。

### <a name="with-specified-hostname"></a>具有指定的主機名稱

您還可以在入口上指定主機名稱，以便對 TLS 配置和服務進行多工。
通過指定主機名稱，留言簿服務將僅在指定的主機上可用。

1. 定義以下入口。
    在入口中，在`secretName`節中指定機密的名稱，並相應地替換節中的`hosts`主機名稱。

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. 按`ing-guestbook-tls-sni.yaml`運行部署

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. 檢查入口控制器的日誌，瞭解部署狀態。

現在，`guestbook`應用程式僅在指定的主機上同時在 HTTP 和 HTTPS 上`<guestbook.contoso.com>`可用（在此示例中）。

## <a name="integrate-with-other-services"></a>與其他服務整合

以下入口將允許您向此入口添加其他路徑，並將這些路徑重定向到其他服務：

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
