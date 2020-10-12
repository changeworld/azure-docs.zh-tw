---
title: 搭配 Azure Kubernetes Service 中部署的微服務使用 Azure API 管理 |Microsoft Docs
description: 本文說明使用 AKS 部署 API 管理的選項
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 5e995d008b441e122f9e93e5f7c29f0bb9bf9c53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254685"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>搭配 Azure Kubernetes Service 中部署的微服務使用 Azure API 管理

微服務非常適合用來建立 Api。 透過 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) ，您可以在雲端中快速部署和操作 [微服務架構的架構](/azure/architecture/guide/architecture-styles/microservices) 。 然後，您可以利用 [AZURE api](https://aka.ms/apimrocks) 管理 (api 管理) 將微服務發佈為內部和外部使用的 api。 本文說明使用 AKS 部署 API 管理的選項。 它假設 Kubernetes、API 管理和 Azure 網路的基本知識。 

## <a name="background"></a>背景

將微服務發佈為 Api 以供取用時，管理微服務和取用用戶端的用戶端之間的通訊可能很困難。 有許多跨領域考慮，例如驗證、授權、節流、快取、轉換和監視。 無論微服務是否公開給內部或外部用戶端，這些考慮都有效。 

[API 閘道](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern)模式會解決這些問題。 API 閘道可做為微服務的前門、將用戶端與您的微服務分開、增加額外的安全性層級，並藉由消除處理跨領域考慮的負擔，來降低微服務的複雜度。 

[AZURE Api 管理](https://aka.ms/apimrocks) 是解決 API 閘道需求的全包式解決方案。 您可以為微服務快速建立一致且現代化的閘道，並將其發佈為 Api。 作為完整生命週期的 API 管理解決方案，它也提供額外的功能，包括 API 探索的自助開發人員入口網站、API 生命週期管理和 API 分析。

搭配使用時，AKS 和 API 管理提供部署、發佈、保護、監視和管理以微服務為基礎之 Api 的平臺。 在本文中，我們將逐步解說幾個部署 AKS 的選項，以搭配 API 管理。 

## <a name="kubernetes-services-and-apis"></a>Kubernetes 服務和 Api

在 Kubernetes 叢集中，容器會部署在 pod 中，這是暫時 [的，且](https://kubernetes.io/docs/concepts/workloads/pods/pod/)具有生命週期。 當背景工作節點中斷時，節點上執行的 pod 會遺失。 因此，Pod 的 IP 位址可以隨時變更。 我們無法依賴它來與 pod 進行通訊。 

為了解決這個問題，Kubernetes 引進了 [服務](https://kubernetes.io/docs/concepts/services-networking/service/)的概念。 Kubernetes 服務是一種抽象層，可定義 pod 的邏輯群組，並啟用這些 pod 的外部流量暴露、負載平衡和服務探索。 

當我們準備好透過 API 管理將微服務發佈為 Api 時，我們需要思考如何將 Kubernetes 中的服務對應至 API 管理中的 Api。 沒有設定的規則。 這取決於您在一開始就如何設計和分割商務功能或網域到微服務中。 比方說，如果服務後方的 pod 負責指定資源上的所有作業 (例如，客戶) ，服務可能會對應到一個 API。 如果資源上的作業分割成多個微服務 (例如 GetOrder、PlaceOrder) ，則多個服務可能會以邏輯方式匯總成 API 管理中一個單一 API (請參閱圖 1) 。 

對應也可以演進。 由於 API 管理會在微服務前方建立外觀，因此我們可以在一段時間內重構和適當地調整微服務的大小。 

![將服務對應至 Api](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>在 AKS 前部署 API 管理

有幾個選項可將 API 管理部署在 AKS 叢集前面。 

雖然 AKS 叢集一律部署在虛擬網路中 (VNet) ，但不需要在 VNet 中部署 API 管理實例。 當 API 管理不在叢集 VNet 內時，AKS 叢集必須發佈公用端點，API 管理才能連接至。 在此情況下，需要保護 API 管理與 AKS 之間的連線。 換句話說，我們需要確保叢集只能透過 API 管理來獨佔存取。 讓我們來看看這些選項。 

### <a name="option-1-expose-services-publicly"></a>選項1：公開公開服務

AKS 叢集中的服務可以使用 NodePort、LoadBalancer 或 ExternalName 的 [服務類型](../aks/concepts-network.md) 公開公開。 在此情況下，服務可直接從公用網際網路存取。 將 API 管理部署在叢集前方之後，我們必須在微服務中套用驗證，以確保所有輸入流量都經過 API 管理。 例如，API 管理可以在對叢集提出的每個要求中包含存取權杖。 每個微服務都必須先負責驗證權杖，然後再處理要求。 


這可能是在 AKS 之前部署 API 管理的最簡單選項，特別是在您的微服務中已實行驗證邏輯的情況下。 

![直接發佈服務](./media/api-management-aks/direct.png)

優點：
* API 管理端的簡單設定，因為它不需要插入叢集 VNet 中
* 如果服務已公開公開且驗證邏輯已經存在於微服務中，AKS 端就不會有任何變更

缺點：
* 因為服務端點的公開可見度，潛在的安全性風險
* 沒有輸入叢集流量的單一進入點
* 使用重複的驗證邏輯使微服務變得更複雜

### <a name="option-2-install-an-ingress-controller"></a>選項2：安裝輸入控制器

雖然選項1可能更容易，但它有明顯的缺點，如下所述。 如果 API 管理實例不在叢集 VNet 中， (mTLS) 的相互 TLS 驗證，是確保流量在 API 管理實例與 AKS 叢集之間雙向安全且受信任的強大方式。 

相互 TLS 驗證是由 API 管理原生 [支援](./api-management-howto-mutual-certificates.md) ，並可在 Kubernetes 中啟用，方法是 [安裝輸入控制器](../aks/ingress-own-tls.md) (圖 3) 。 因此，驗證將會在輸入控制器中執行，以簡化微服務。 此外，您可以透過輸入將 API 管理的 IP 位址新增至允許清單，以確保只有 API 管理可存取叢集。  

 
![透過輸入控制器發佈](./media/api-management-aks/ingress-controller.png)


優點：
* API 管理端的簡單設定，因為它不需要插入叢集 VNet 中，而是原生支援的 mTLS
* 集中保護輸入控制器層的輸入叢集流量
* 將公開可見的叢集端點降至最低，以降低安全性風險

缺點：
* 增加叢集設定的複雜度，因為安裝、設定和維護輸入控制器，以及管理用於 mTLS 的憑證的額外工作
* 因為輸入控制器端點 (的公開可見度，所以有安全性風險) 


透過 APIM 發佈 API 時，安全地存取這些 API 的簡單常用方式是使用訂用帳戶金鑰。 需要使用已發佈 API 的開發人員在呼叫這些 API 時，必須在 HTTP 要求中包含有效的訂用帳戶金鑰。 否則，呼叫會立即遭到 APIM 閘道的拒絕。 這些呼叫並不會轉送到後端服務。

若要取得用於存取 API 的訂用帳戶金鑰，則需要訂用帳戶。 訂用帳戶基本上是一組訂用帳戶金鑰的具名容器。 需要取用已發行 API 的開發人員可以取得訂用帳戶。 而且不需要 API 發行者的核准。 API 發行者還可以為 API 取用者直接建立訂用帳戶。

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>選項3：在叢集 VNet 內部署 APIM

在某些情況下，具有法規條件約束或嚴格安全性需求的客戶，可能會發現選項1和2並非可行的解決方案，因為公開的端點。 在其他情況下，AKS 叢集和取用微服務的應用程式可能位於相同的 VNet 中，因此沒有理由公開叢集，因為所有 API 流量都會保留在 VNet 內。 在這些案例中，您可以將 API 管理部署到叢集 VNet。 [API 管理高階層](https://aka.ms/apimpricing) 支援 VNet 部署。 

有兩種模式可將 [API 管理部署至 VNet](./api-management-using-with-vnet.md) –外部和內部。 

如果 API 取用者不在叢集 VNet 中，則應該使用外部模式 (圖 4) 。 在此模式中，會將 API 管理閘道插入叢集 VNet 中，但可透過外部負載平衡器從公用網際網路存取。 它有助於完全隱藏叢集，同時仍然允許外部用戶端使用微服務。 此外，您可以使用 Azure 網路功能，例如網路安全性群組 (NSG) 來限制網路流量。

![外部 VNet 模式](./media/api-management-aks/vnet-external.png)

如果所有 API 取用者都位於叢集 VNet 內，則可以使用內部模式 (圖 5) 。 在此模式中，會將 API 管理閘道插入叢集 VNET，而且只能透過內部負載平衡器從此 VNet 中存取。 沒有任何方法可從公用網際網路連線到 API 管理閘道或 AKS 叢集。 

![內部 VNet 模式](./media/api-management-aks/vnet-internal.png)

 在這兩種情況下，AKS 叢集都不會公開顯示。 相較于選項2，輸入控制器可能不是必要的。 視您的案例和設定而定，API 管理與您的微服務之間可能仍需要進行驗證。 比方說，如果採用服務網格，則一律需要相互 TLS 驗證。 

優點：
* 最安全的選項，因為 AKS 叢集沒有公用端點
* 簡化叢集設定，因為它沒有公用端點
* 能夠使用內部模式隱藏 VNet 內的 API 管理和 AKS
* 使用 Azure 網路功能（例如 (NSG 的網路安全性群組）來控制網路流量的能力) 

缺點：
* 增加部署和設定 API 管理以在 VNet 內運作的複雜度

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解 [AKS 中應用程式的網路概念](../aks/concepts-network.md)
* 深入瞭解 [如何搭配使用 API 管理與虛擬網路](./api-management-using-with-vnet.md)
