---
title: 將 Azure API 管理與部署在 Azure 庫伯奈斯服務中的微服務結合使用 |微軟文檔
description: 本文介紹了使用 AKS 部署 API 管理的選項
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
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480992"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>將 Azure API 管理與部署在 Azure 庫伯奈斯服務中的微服務一起使用

微服務非常適合構建 API。 借助[Azure 庫伯奈斯服務](https://azure.microsoft.com/services/kubernetes-service/)（AKS），您可以在雲中快速部署和操作[基於微服務的體系結構](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices)。 然後，可以利用[Azure API 管理](https://aka.ms/apimrocks)（API 管理）將微服務發佈為 API，以便內部和外部使用。 本文介紹了使用 AKS 部署 API 管理的選項。 它假定 Kubernetes、API 管理和 Azure 網路的基本知識。 

## <a name="background"></a>背景

將微服務發佈為 API 以進行消費時，管理微服務與使用它們的用戶端之間的通信可能具有挑戰性。 存在許多貫穿各領域的問題，如身份驗證、授權、限制、緩存、轉換和監視。 無論微服務是向內部用戶端還是外部用戶端公開，這些顧慮都有效。 

[API 閘道](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern)模式解決了這些問題。 API 閘道充當微服務的前門，將用戶端與微服務分離，增加一層安全性，並通過消除處理交叉問題的負擔來降低微服務的複雜性。 

[Azure API 管理](https://aka.ms/apimrocks)是解決 API 閘道需求的交鑰匙解決方案。 您可以快速為微服務創建一致且現代的閘道，並將其發佈為 API。 作為全生命週期 API 管理解決方案，它還提供了其他功能，包括用於 API 發現、API 生命週期管理和 API 分析的自助服務開發人員門戶。

當同時使用時，AKS 和 API 管理提供了一個平臺，用於部署、發佈、保護、監視和管理基於微服務的 API。 在本文中，我們將介紹一些與 API 管理一起部署 AKS 的選項。 

## <a name="kubernetes-services-and-apis"></a>庫伯奈斯服務和 API

在 Kubernetes 群集中，容器部署在[Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/)中，這些容器是短暫的，並且具有生命週期。 當輔助節點死亡時，在節點上運行的 Pod 將丟失。 因此，Pod 的 IP 位址可以隨時更改。 我們不能依靠它與吊艙通信。 

為了解決這個問題，庫伯內斯引入了[服務](https://kubernetes.io/docs/concepts/services-networking/service/)的概念。 Kubernetes 服務是一個抽象層，它定義一個 Pod 的邏輯組，並為這些 Pod 提供外部流量暴露、負載平衡和服務發現。 

當我們準備好通過 API 管理將微服務作為 API 發佈時，我們需要考慮如何將庫伯奈斯中的服務映射到 API 管理中的 API。 沒有設置的規則。 這取決於您一開始就如何設計和將業務功能或域分區為微服務。 例如，如果服務後面的窗格負責給定資源（例如客戶）的所有操作，則服務可能映射到一個 API。 如果將資源上的操作劃分為多個微服務（例如 GetOrder、PlaceOrder），則多個服務可以在 API 管理中邏輯聚合到單個 API 中（參見圖 1）。 

映射也可以發展。 由於 API 管理在微服務前面創建了一個立面，因此隨著時間的推移，它使我們能夠重構和調整微服務的大小。 

![將服務映射到 API](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>在 AKS 前面部署 API 管理

在 AKS 群集前面部署 API 管理有幾個選項。 

雖然 AKS 群集始終部署在虛擬網路 （VNet） 中，但 API 管理實例不需要在 VNet 中部署。 當 API 管理不駐留在群集 VNet 中時，AKS 群集必須發佈公共終結點，以便 API 管理連接到。 在這種情況下，需要保護 API 管理和 AKS 之間的連接。 換句話說，我們需要確保只能通過 API 管理專門訪問群集。 讓我們來討論一下這些選項。 

### <a name="option-1-expose-services-publicly"></a>選項 1：公開公開服務

可以使用節點埠、負載平衡器或外部名稱[的服務類型](https://docs.microsoft.com/azure/aks/concepts-network)公開公開 AKS 群集中的服務。 在這種情況下，服務可以直接從公共互聯網訪問。 在群集前面部署 API 管理後，我們需要通過在微服務中應用身份驗證來確保所有入站流量都通過 API 管理。 例如，API 管理可以在對群集的每個請求中包含一個訪問權杖。 每個微服務負責在處理請求之前驗證權杖。 


這可能是在 AKS 前面部署 API 管理最簡單的選項，尤其是在微服務中已經實現了身份驗證邏輯時。 

![直接發佈服務](./media/api-management-aks/direct.png)

優點：
* API 管理端的配置簡單，因為它不需要注入群集 VNet
* 如果服務已公開公開且微服務中已存在身份驗證邏輯，則 AKS 端沒有更改

缺點：
* 由於服務終結點的公共可見度，存在潛在的安全風險
* 入站群集流量沒有單進入點
* 使用重複身份驗證邏輯使微服務複雜化

### <a name="option-2-install-an-ingress-controller"></a>選項 2：安裝入口控制器

雖然選項 1 可能更容易，但它具有上述明顯的缺點。 如果 API 管理實例不駐留在群集 VNet 中，則相互 TLS 身份驗證 （mTLS） 是確保流量在 API 管理實例和 AKS 群集之間的雙向可靠和受信任的可靠方法。 

互 TLS 身份驗證由 API 管理[本機支援](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)，可以通過[安裝入口控制器](https://docs.microsoft.com/azure/aks/ingress-own-tls)在庫伯內斯啟用（圖 3）。 因此，身份驗證將在入口控制器中執行，從而簡化了微服務。 此外，您可以將 API 管理的 IP 位址添加到入口允許的清單中，以確保只有 API 管理才能訪問群集。  

 
![通過入口控制器發佈](./media/api-management-aks/ingress-controller.png)


優點：
* API 管理端的易於配置，因為它不需要注入群集 VNet，並且 mTLS 受本機支援
* 在入口控制器層集中保護入站群集流量
* 通過最小化公開可見的群集終結點來降低安全風險

缺點：
* 由於安裝、配置和維護入口控制器和管理用於 mTLS 的證書的額外工作，增加了群集配置的複雜性
* 入口控制器終結點的公共可見度導致安全風險


透過 APIM 發佈 API 時，安全地存取這些 API 的簡單常用方式是使用訂用帳戶金鑰。 需要使用已發佈 API 的開發人員在呼叫這些 API 時，必須在 HTTP 要求中包含有效的訂用帳戶金鑰。 否則，呼叫會立即遭到 APIM 閘道的拒絕。 這些呼叫並不會轉送到後端服務。

若要取得用於存取 API 的訂用帳戶金鑰，則需要訂用帳戶。 訂用帳戶基本上是一組訂用帳戶金鑰的具名容器。 需要取用已發行 API 的開發人員可以取得訂用帳戶。 而且不需要 API 發行者的核准。 API 發行者還可以為 API 取用者直接建立訂用帳戶。

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>選項 3：在群集 VNet 中部署 APIM

在某些情況下，具有法規約束或嚴格安全要求的客戶可能會發現選項 1 和選項 2 由於公開公開的終結點而不可行的解決方案。 在另一些方面，AKS 群集和使用微服務的應用程式可能駐留在同一 VNet 中，因此沒有理由公開群集，因為所有 API 流量都將保留在 VNet 中。 對於這些方案，可以將 API 管理部署到群集 VNet 中。 [API 管理高級層](https://aka.ms/apimpricing)支援 VNet 部署。 

將[API 管理部署到 VNet](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)和外部和內部有兩種模式。 

如果 API 消費者不駐留在群集 VNet 中，則應使用外部模式（圖 4）。 在此模式下，API 管理閘道被注入群集 VNet，但可通過外部負載等化器從公共 Internet 訪問。 它有助於完全隱藏群集，同時仍然允許外部用戶端使用微服務。 此外，還可以使用 Azure 網路功能（如網路安全性群組 （NSG） 來限制網路流量。

![外部 VNet 模式](./media/api-management-aks/vnet-external.png)

如果所有 API 消費者都駐留在群集 VNet 中，則可以使用內部模式（圖 5）。 在此模式下，API 管理閘道被注入群集 VNET，並且只能通過內部負載等化器在此 VNet 中訪問。 無法從公共 Internet 到達 API 管理閘道或 AKS 群集。 

![內部 VNet 模式](./media/api-management-aks/vnet-internal.png)

 在這兩種情況下，AKS 群集都不可見。 與選項 2 相比，入口控制器可能不需要。 根據您的方案和配置，API 管理和微服務之間可能仍然需要身份驗證。 例如，如果採用服務網格，它始終需要相互 TLS 身份驗證。 

優點：
* 最安全的選項，因為 AKS 群集沒有公共終結點
* 簡化群集配置，因為它沒有公共終結點
* 使用內部模式將 API 管理和 AKS 隱藏在 VNet 中的能力
* 使用 Azure 網路功能（如網路安全性群組 （NSG））控制網路流量的能力

缺點：
* 提高在 VNet 內部署和配置 API 管理的複雜性

## <a name="next-steps"></a>後續步驟

* 瞭解有關[AKS 中應用的網路概念的更多資訊](https://docs.microsoft.com/azure/aks/concepts-network)
* 瞭解有關如何使用[API 管理與虛擬網路](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)有關





