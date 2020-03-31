---
title: 什麼是 Azure 應用程式閘道入口控制器？
description: 本文介紹了什麼是應用程式閘道入口控制器。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335806"
---
# <a name="what-is-application-gateway-ingress-controller"></a>什麼是應用程式閘道入口控制器？
應用程式閘道入口控制器 （AGIC） 是 Kubernetes 應用程式，它使 Azure[庫伯奈斯服務 （AKS）](https://azure.microsoft.com/services/kubernetes-service/)客戶能夠利用 Azure 的本機[應用程式閘道](https://azure.microsoft.com/services/application-gateway/)L7 負載等化器將雲軟體公開到 Internet。 AGIC 監視託管在 Kubernetes 群集，並持續更新應用程式閘道，以便所選服務暴露給 Internet。

入口控制器在客戶的 AKS 上自己的窗格中運行。 AGIC 監視庫伯內斯資源的資源子集以進行更改。 AKS 群集的狀態將轉換為應用程式閘道特定配置，並應用於[Azure 資源管理器 （ARM）。](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="benefits-of-application-gateway-ingress-controller"></a>應用閘道入口控制器的優勢
AGIC 允許您部署使用單個應用程式閘道入口控制器控制多個 AKS 群集。 AGIC 還有助於消除在 AKS 群集前面使用另一個負載等化器/公共 IP 的需要，並在請求到達 AKS 群集之前避免資料路徑中的多個躍點。 應用程式閘道直接使用其專用 IP 與 Pod 對話，不需要 NodePort 或 KubeProxy 服務。 這也為您的部署帶來了更好的性能。

入口控制器僅受Standard_v2和WAF_v2 SKU 的支援，這也帶來了自動縮放的優勢。 應用程式閘道可以回應流量負載的增加或減少並相應地擴展，而無需消耗來自 AKS 群集的任何資源。

除了 AGIC 之外，使用應用程式閘道還通過提供 TLS 策略和 Web 應用程式防火牆 （WAF） 功能，説明保護您的 AKS 群集。

![Azure 應用程式閘道 + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC 通過庫伯內斯[入口資源](https://kubernetes.io/docs/user-guide/ingress/)以及服務和部署/Pods 進行配置。 它利用 Azure 的本機應用程式閘道 L7 負載等化器提供了許多功能。 以下提供幾個範例：
  - URL 路由
  - 以 Cookie 為基礎的同質性
  - TLS 終止
  - 端到端 TLS
  - 支援公共、私有和混合網站
  - 集成 Web 應用程式防火牆

AGIC 能夠處理多個命名空間，並且具有禁止目標，這意味著 AGIC 可以專門為 AKS 群集配置應用程式閘道，而不會影響其他現有後端。 

## <a name="next-steps"></a>後續步驟

- [**綠地部署**](ingress-controller-install-new.md)：有關在空白板式基礎結構上安裝 AGIC、AKS 和應用程式閘道的說明。
- [**布朗菲爾德部署**](ingress-controller-install-existing.md)：在現有 AKS 和應用程式閘道上安裝 AGIC。

