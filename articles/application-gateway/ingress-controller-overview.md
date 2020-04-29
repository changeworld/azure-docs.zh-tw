---
title: 什麼是 Azure 應用程式閘道輸入控制器？
description: 本文提供應用程式閘道輸入控制器的簡介。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335806"
---
# <a name="what-is-application-gateway-ingress-controller"></a>什麼是應用程式閘道輸入控制器？
應用程式閘道輸入控制器（AGIC）是 Kubernetes 應用程式，可讓[Azure Kubernetes Service （AKS）](https://azure.microsoft.com/services/kubernetes-service/)客戶利用 Azure 的原生[應用程式閘道](https://azure.microsoft.com/services/application-gateway/)L7 負載平衡器，將雲端軟體公開到網際網路。 AGIC 會監視其裝載所在的 Kubernetes 叢集，並持續更新應用程式閘道，讓選取的服務向網際網路公開。

輸入控制器會在客戶的 AKS 上，于自己的 pod 中執行。 AGIC 會監視 Kubernetes 資源的子集，以進行變更。 AKS 叢集的狀態會轉譯為應用程式閘道特定設定，並套用至[Azure Resource Manager （ARM）](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

## <a name="benefits-of-application-gateway-ingress-controller"></a>應用程式閘道輸入控制器的優點
AGIC 可讓您的部署使用單一應用程式閘道輸入控制器來控制多個 AKS 叢集。 AGIC 也有助於避免在 AKS 叢集之前有另一個負載平衡器/公用 IP 的需求，並在要求到達 AKS 叢集之前，避免資料路徑中的多個躍點。 應用程式閘道使用其私人 IP 直接與 pod 交談，而不需要 NodePort 或 KubeProxy 服務。 這也為您的部署帶來了更好的效能。

Standard_v2 和 WAF_v2 Sku 僅支援輸入控制器，這也會為您帶來自動調整的優勢。 應用程式閘道可以回應流量負載增加或減少，並據以調整，而不會耗用 AKS 叢集的任何資源。

除了 AGIC 之外，使用應用程式閘道也會提供 TLS 原則和 Web 應用程式防火牆（WAF）功能，協助保護您的 AKS 叢集。

![Azure 應用程式閘道 + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC 是透過 Kubernetes 輸入[資源](https://kubernetes.io/docs/user-guide/ingress/)，以及服務和部署/pod 來設定。 它提供許多功能，利用 Azure 的原生應用程式閘道 L7 負載平衡器。 以下提供幾個範例：
  - URL 路由
  - 以 Cookie 為基礎的同質性
  - TLS 終止
  - 端對端 TLS
  - 支援公用、私用和混合式網站
  - 整合式 web 應用程式防火牆

AGIC 能夠處理多個命名空間並具有 ProhibitedTargets，這表示 AGIC 可以特別為 AKS 叢集設定應用程式閘道，而不會影響其他現有的後端。 

## <a name="next-steps"></a>後續步驟

- [**Greenfield 部署**](ingress-controller-install-new.md)：在空白平板電腦基礎結構上安裝 AGIC、AKS 和應用程式閘道的指示。
- [**Brownfield 部署**](ingress-controller-install-existing.md)：在現有的 AKS 上安裝 AGIC，並應用程式閘道。

