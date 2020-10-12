---
title: 什麼是閘道輸入控制器 Azure 應用程式？
description: 本文提供應用程式閘道輸入控制器的簡介。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: c1bd41587e4f56fb0a7f3eb8285d301751f558d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84668095"
---
# <a name="what-is-application-gateway-ingress-controller"></a>什麼是應用程式閘道輸入控制器？
應用程式閘道輸入控制器 (AGIC) 是一種 Kubernetes 應用程式，可讓 [Azure Kubernetes Service (AKS) ](https://azure.microsoft.com/services/kubernetes-service/) 客戶利用 Azure 的原生 [應用程式閘道](https://azure.microsoft.com/services/application-gateway/) L7 負載平衡器，向網際網路公開雲端軟體。 AGIC 會監視裝載的 Kubernetes 叢集，並持續更新應用程式閘道，讓選取的服務公開至網際網路。

輸入控制器會在客戶的 AKS 上以自己的 pod 執行。 AGIC 會監視 Kubernetes 資源的子集以進行變更。 AKS 叢集的狀態會轉譯為應用程式閘道特定設定，並套用至 [Azure Resource Manager (ARM) ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

## <a name="benefits-of-application-gateway-ingress-controller"></a>應用程式閘道輸入控制器的優點
AGIC 可協助消除在 AKS 叢集前面有其他負載平衡器/公用 IP 的需求，並在要求抵達 AKS 叢集之前，避免資料路徑中的多個躍點。 應用程式閘道會直接與使用其私人 IP 的 pod 交談，而不需要 NodePort 或 KubeProxy 服務。 這也可為您的部署帶來更好的效能。

輸入控制器僅受 Standard_v2 和 WAF_v2 Sku 所支援，也提供您自動調整的優點。 應用程式閘道可以回應流量負載的增加或減少，並據此調整，而不需要從 AKS 叢集使用任何資源。

除了 AGIC 之外，使用應用程式閘道也可提供 TLS 原則和 Web 應用程式防火牆 (WAF) 功能，以協助保護您的 AKS 叢集。

![Azure 應用程式 Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC 是透過 Kubernetes 輸入 [資源](https://kubernetes.io/docs/user-guide/ingress/)，以及服務和部署/pod 來設定。 它提供一些功能，可運用 Azure 的原生應用程式閘道 L7 負載平衡器。 以下提供幾個範例：
  - URL 路由
  - 以 Cookie 為基礎的同質性
  - TLS 終止
  - 端對端 TLS
  - 支援公用、私用和混合式網站
  - 整合式 web 應用程式防火牆

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Helm 部署和 AKS Add-On 之間的差異
有兩種方式可以為您的 AKS 叢集部署 AGIC。 第一種方式是透過 Helm;第二個是透過 AKS 作為附加元件。 將 AGIC 部署為 AKS 附加元件的主要優點是，它比透過 Helm 部署更為簡單。 針對新的安裝程式，您可以部署新的應用程式閘道和新的 AKS 叢集，並將 AGIC 啟用為 Azure CLI 中一行的附加元件。 附加元件也是完全受控的服務，可提供額外的優點，例如自動更新和增加的支援。 AKS 不支援透過 Helm 部署的 AGIC，但是 AKS 會支援部署為 AKS 附加元件的 AGIC。 

AGIC 附加元件仍會部署為客戶 AKS 叢集中的 pod，不過 Helm 部署版本與 AGIC 附加元件版本之間有一些差異。 以下是兩個版本之間的差異清單： 
  - Helm 部署值無法在 AKS 附加元件上修改：
    - `verbosityLevel` 預設會設定為5
    - `usePrivateIp`預設會設定為 false;這可由[使用-私用-ip 注釋](ingress-controller-annotations.md#use-private-ip)覆寫
    - `shared` 附加元件不支援 
    - `reconcilePeriodSeconds` 附加元件不支援
    - `armAuth.type` 附加元件不支援
  - 透過 Helm 部署的 AGIC 支援 ProhibitedTargets，這表示 AGIC 可以針對 AKS 叢集明確地設定應用程式閘道，而不會影響其他現有的後端。 AGIC 附加元件目前不支援此操作。 
  - 由於 AGIC 附加元件是受管理的服務，因此客戶將會自動更新為最新版本的 AGIC 附加元件，與透過 Helm 部署的 AGIC （客戶必須手動更新 AGIC）不同。 

> [!NOTE]
> 部署的 AGIC AKS 附加元件方法目前為預覽狀態。 我們不建議您在仍處於預覽狀態的功能上執行生產工作負載，所以如果您想要試試看，建議您設定新的叢集，以使用進行測試。 

下表將排序 Helm 部署版本和 AGIC 的 AKS 附加元件版本目前支援的案例。 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS 附加元件 AGIC (單一 AKS 叢集) 
|                  |1個應用程式閘道 |2個以上的應用程式閘道 |
|------------------|---------|--------|
|**1 AGIC**|是，這是支援的 |否，這是在我們的待處理專案中 |
|**2 + AGICs**|否，只有1個 AGIC 支援/叢集 |否，只有1個 AGIC 支援/叢集 |

### <a name="helm-deployed-agic-single-aks-cluster"></a>Helm 已部署的 AGIC (單一 AKS 叢集) 
|                  |1個應用程式閘道 |2個以上的應用程式閘道 |
|------------------|---------|--------|
|**1 AGIC**|是，這是支援的 |否，這是在我們的待處理專案中 |
|**2 + AGICs**|必須使用共用的 ProhibitedTarget 功能並監看不同的命名空間 |是，這是支援的 |

### <a name="helm-deployed-agic-2-aks-clusters"></a>Helm 部署的 AGIC (2 + AKS 叢集) 
|                  |1個應用程式閘道 |2個以上的應用程式閘道 |
|------------------|---------|--------|
|**1 AGIC**|N/A |N/A |
|**2 + AGICs**|必須使用共用 ProhibitedTarget 功能 |N/A |

## <a name="next-steps"></a>後續步驟
- [**AKS Add-On Greenfield 部署**](tutorial-ingress-controller-add-on-new.md)：在空白-平板基礎結構上安裝 AGIC 附加元件、AKS 和應用程式閘道的指示。
- [**AKS Add-On 棕色地帶部署**](tutorial-ingress-controller-add-on-existing.md)：在具有現有應用程式閘道的 AKS 叢集上安裝 AGIC 附加元件。
- [**Helm Greenfield 部署**](ingress-controller-install-new.md)：透過 Helm、新的 AKS 叢集和全新的應用程式閘道安裝 AGIC 到空白的平板基礎結構。
- [**Helm 棕色地帶部署**](ingress-controller-install-existing.md)：在現有的 AKS 叢集和應用程式閘道上透過 HELM 部署 AGIC。

