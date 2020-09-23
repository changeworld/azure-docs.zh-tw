---
title: Azure 資訊安全中心和 Azure Container Registry
description: 瞭解如何使用 Azure 資訊安全中心來掃描您的容器登錄
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 1335b1034304b7efe2b113f7ff2d2927fea41638
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977365"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Azure Container Registry 由安全中心掃描影像

Azure Container Registry (ACR) 是受控的私人 Docker 登錄服務，可在中央登錄中儲存及管理 Azure 部署的容器映射。 它是以開放原始碼的 Docker Registry 2.0 為基礎。

針對 **容器登錄啟用 Azure Defender** ，以便更深入瞭解 Azure Resource Manager 型登錄中的映射弱點。 在訂用帳戶層級啟用或停用方案，以涵蓋訂用帳戶中的所有登錄。 這項功能是依每個映射計費，如 [ [定價] 頁面](security-center-pricing.md)所示。 啟用 Azure Defender，可確保安全性中心已準備好掃描推送至登錄的映射。 


## <a name="when-are-images-scanned"></a>影像掃描的時間為何？

每次將映射推送至您的登錄時，安全性中心就會自動掃描該映射。 若要觸發映射掃描，請將其推送至您的存放庫。

當掃描完成時 (通常是在大約2分鐘之後，但最多可能會有15分鐘的) ，這些結果會以安全性中心建議的形式提供，如下所示：

[![有關在 Azure Container Registry (ACR) 託管映射中探索到的弱點 Azure 資訊安全中心建議](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>整合的優點

安全性中心會識別您訂用帳戶中以 Azure Resource Manager 為基礎的 ACR 登錄，並無縫提供：

* **Azure-原生弱點掃描** 所有已推送的 Linux 映射。 「安全性中心」會使用來自領先業界的弱點掃描廠商 Qualys 的掃描器來掃描映射。 此原生解決方案預設為緊密整合。

* 具有已知弱點之 Linux 映射的**安全性建議**。 資訊安全中心會提供每個報告的弱點和嚴重性分類的詳細資料。 此外，它也提供如何補救每個推送至登錄的映射上所發現之特定弱點的指引。

![Azure 資訊安全中心和 Azure Container Registry (ACR) 高階總覽](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure Container Registry 影像掃描的常見問題

### <a name="how-does-security-center-scan-an-image"></a>資訊安全中心如何掃描映射？
從登錄中提取映射。 然後，它會在隔離的沙箱中執行，並使用 Qualys 掃描器來解壓縮已知的弱點清單。

安全性中心會篩選並分類掃描器的結果。 當映射狀況良好時，「安全性中心」會將它標示為如此。 安全性中心只會針對有問題要解決的映射產生安全性建議。 藉由只在發生問題時通知，資訊安全中心會降低不必要資訊警示的可能性。

### <a name="how-often-does-security-center-scan-my-images"></a>資訊安全中心掃描我的映射的頻率為何？
每次推送時都會觸發影像掃描。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>我可以透過 REST API 取得掃描結果嗎？
可以。 結果會在 [子評量 REST API](/rest/api/securitycenter/subassessments/list/)下進行。 此外，您也可以使用 Azure Resource Graph (ARG) （適用于所有資源的 Kusto） API：查詢可以提取特定的掃描。
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>掃描的登錄類型為何？ 哪些類型會計費？
[可用性] 區段會列出 Azure Defender for container registry 所支援的容器登錄類型。 

如果不支援的登錄會連接到您的 Azure 訂用帳戶，則不會進行掃描，也不會向您收取費用。


## <a name="next-steps"></a>下一步

若要深入瞭解資訊安全中心的容器安全性功能，請參閱：

* [Azure 資訊安全中心與容器安全性](container-security.md)

* [與 Azure Kubernetes Service 整合](azure-kubernetes-service-integration.md)


