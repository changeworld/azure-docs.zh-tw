---
title: 什麼是 Azure 應用程式閘道輸入控制器？
description: 本文提供應用程式閘道輸入控制器的簡介。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: c1bd41587e4f56fb0a7f3eb8285d301751f558d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668095"
---
# <a name="what-is-application-gateway-ingress-controller"></a>什麼是應用程式閘道輸入控制器？
應用程式閘道輸入控制器（AGIC）是 Kubernetes 應用程式，可讓[Azure Kubernetes Service （AKS）](https://azure.microsoft.com/services/kubernetes-service/)客戶利用 Azure 的原生[應用程式閘道](https://azure.microsoft.com/services/application-gateway/)L7 負載平衡器，將雲端軟體公開到網際網路。 AGIC 會監視其裝載所在的 Kubernetes 叢集，並持續更新應用程式閘道，讓選取的服務向網際網路公開。

輸入控制器會在客戶的 AKS 上，于自己的 pod 中執行。 AGIC 會監視 Kubernetes 資源的子集，以進行變更。 AKS 叢集的狀態會轉譯為應用程式閘道特定設定，並套用至[Azure Resource Manager （ARM）](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

## <a name="benefits-of-application-gateway-ingress-controller"></a>應用程式閘道輸入控制器的優點
AGIC 有助於避免在 AKS 叢集前方具有另一個負載平衡器/公用 IP 的需求，並在要求到達 AKS 叢集之前，避免資料路徑中的多個躍點。 應用程式閘道使用其私人 IP 直接與 pod 交談，而不需要 NodePort 或 KubeProxy 服務。 這也為您的部署帶來了更好的效能。

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

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Helm 部署與 AKS 附加元件之間的差異
有兩種方式可為您的 AKS 叢集部署 AGIC。 第一種方式是透過 Helm;第二個是透過 AKS 做為附加元件。 將 AGIC 部署為 AKS 附加元件的主要優點是，它比透過 Helm 部署簡單許多。 針對新的安裝，您可以部署新的應用程式閘道和新的 AKS 叢集，並將 AGIC 啟用為 Azure CLI 中一行的附加元件。 附加元件也是完全受控的服務，它提供了額外的優點，例如自動更新和增加的支援。 AKS 不支援透過 Helm 部署的 AGIC，不過，AKS 會支援部署為 AKS 附加元件的 AGIC。 

AGIC 附加元件仍會在客戶的 AKS 叢集中部署為 pod，不過，Helm 部署版本與 AGIC 的附加元件版本之間有一些差異。 以下是兩個版本之間的差異清單： 
  - 無法在 AKS 附加元件上修改 Helm 部署值：
    - `verbosityLevel`預設會設定為5
    - `usePrivateIp`預設會設定為 false;這可能會被[使用-私用 ip 注釋](ingress-controller-annotations.md#use-private-ip)覆寫
    - `shared`附加元件上不支援 
    - `reconcilePeriodSeconds`附加元件上不支援
    - `armAuth.type`附加元件上不支援
  - 透過 Helm 部署的 AGIC 支援 ProhibitedTargets，這表示 AGIC 可以特別針對 AKS 叢集設定應用程式閘道，而不會影響其他現有的後端。 AGIC 附加元件目前不支援此操作。 
  - 由於 AGIC 附加元件是受控服務，因此客戶會自動更新為最新版本的 AGIC 附加元件，不同于透過 Helm 部署的 AGIC，客戶必須手動更新 AGIC。 

> [!NOTE]
> 部署的 AGIC AKS 附加元件方法目前為預覽狀態。 我們不建議您在仍處於預覽狀態的功能上執行生產工作負載，因此如果您想要試用，建議您設定新的叢集，以測試它。 

下表排序 Helm 部署版本和 AGIC 的 AKS 附加元件版本目前支援的案例。 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS 附加元件 AGIC （單一 AKS 叢集）
|                  |1應用程式閘道 |2個以上的應用程式閘道 |
|------------------|---------|--------|
|**1 AGIC**|是，支援這種情況 |否，這是在我們的待處理專案中 |
|**2 + AGICs**|否，只有1個 AGIC 支援/叢集 |否，只有1個 AGIC 支援/叢集 |

### <a name="helm-deployed-agic-single-aks-cluster"></a>Helm 部署的 AGIC （單一 AKS 叢集）
|                  |1應用程式閘道 |2個以上的應用程式閘道 |
|------------------|---------|--------|
|**1 AGIC**|是，支援這種情況 |否，這是在我們的待處理專案中 |
|**2 + AGICs**|必須使用共用的 ProhibitedTarget 功能並監看個別的命名空間 |是，支援這種情況 |

### <a name="helm-deployed-agic-2-aks-clusters"></a>Helm 部署的 AGIC （2 + AKS 叢集）
|                  |1應用程式閘道 |2個以上的應用程式閘道 |
|------------------|---------|--------|
|**1 AGIC**|不適用 |N/A |
|**2 + AGICs**|必須使用共用的 ProhibitedTarget 功能 |N/A |

## <a name="next-steps"></a>後續步驟
- [**AKS 附加元件 Greenfield 部署**](tutorial-ingress-controller-add-on-new.md)：在空白平板電腦基礎結構上安裝 AGIC 附加元件、AKS 和應用程式閘道的指示。
- [**AKS 附加元件 Brownfield 部署**](tutorial-ingress-controller-add-on-existing.md)：在具有現有應用程式閘道的 AKS 叢集上安裝 AGIC 附加元件。
- [**Helm Greenfield Deployment**](ingress-controller-install-new.md)：透過 Helm、新的 AKS 叢集和新的應用程式閘道安裝在空白平板電腦基礎結構上。
- [**Helm Brownfield Deployment**](ingress-controller-install-existing.md)：在現有的 AKS 叢集上透過 HELM 部署 AGIC，並應用程式閘道。

