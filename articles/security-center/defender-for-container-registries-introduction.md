---
title: 適用於容器登錄的 Azure Defender - 優點和功能
description: 了解適用於容器登錄的 Azure Defender 有何優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 989115224489ff8fff360a34b27c338ea3e33058
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145815"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>適用於容器登錄的 Azure Defender 簡介

Azure Container Registry (ACR) 是受控的私人 Docker 登錄服務，可在中央登錄中儲存及管理您 Azure 部署的容器映像。 此服務以開放原始碼的 Docker Registry 2.0 為基礎。

若要保護您訂用帳戶中所有以 Azure Resource Manager 為基礎的登錄，請在訂用帳戶層級上啟用 **適用於容器登錄的 Azure Defender** 。 資訊安全中心接著將會掃描推送至登錄的映像、匯入登錄中的映像，或過去 30 天內提取的任何映像。 這項功能會按映像收費。

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>適用於容器登錄的 Azure Defender 有哪些優點？

資訊安全中心可識別您的訂用帳戶中以 Azure Resource Manager 為基礎的 ACR 登錄，並順暢地為您的登錄映像提供 Azure 原生弱點評量和管理。

**適用於容器登錄的 Azure Defender** 包含弱點掃描器，可在您以 Azure Resource Manager 為基礎的 Azure Container Registry 登錄中掃描映像，並且讓您更深入地檢視映像的弱點。 整合式掃描器由領先業界的弱點掃描廠商 Qualys 提供技術支援。

當 Qualys 或資訊安全中心發現問題時，您將會在資訊安全中心儀表板中收到通知。 針對每個弱點，資訊安全中心都會提供可採取動作的建議及嚴重性分類，以及如何補救問題的指引。 如需資訊安全中心的容器建議詳細資料，請參閱[建議的參考清單](recommendations-reference.md#recs-containers)。

資訊安全中心會篩選並分類掃描器發現的結果。 當映像狀況良好時，資訊安全中心會據以標示。 資訊安全中心只會針對有問題待解決的映像產生安全性建議。 資訊安全中心會提供每個回報的弱點和嚴重性分類的詳細資料。 此外也會提供如何對在每個映像上發現的特定弱點進行補救的指引。

資訊安全中心只會在發生問題時發出通知，藉以減少非必要的資訊警示。


> [!TIP]
> 若要深入了解資訊安全中心的容器安全性功能，請參閱：
>
> - [Azure 資訊安全中心和容器安全性](container-security.md)
> - [適用於 Kubernetes 的 Azure Defender 簡介](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>何時會掃描映像？

映像掃描有三個觸發程序：

- **推送時** - 每當映像推送至您的登錄時，資訊安全中心就會自動掃描該映像。 若要觸發映像的掃描，請將其推送至您的存放庫。

- **最近提取的** - 由於每天都會發現新的弱點， **適用於容器登錄的 Azure Defender** 也會掃描過去 30 天內提取的任何映像。 重新掃描不會額外收費；如前所述，您需按映像付費。

- **匯入時** - Azure Container Registry 具有匯入工具，可將映像從 Docker Hub、Microsoft Container Registry 或另一個 Azure Container Registry 導入您的登錄中。 **適用於容器登錄的 Azure Defender** 會掃描您匯入的任何支援映像。 深入了解如何[將容器映像匯入至容器登錄](../container-registry/container-registry-import-images.md)。
 
掃描通常會在 2 分鐘內完成，但最多可能需要 15 分鐘的時間。 結果會以資訊安全中心建議的形式提供，如下所示：

[![Azure 資訊安全中心針對在 Azure Container Registry (ACR) 裝載映像中探索到的弱點提供的建議範例](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>資訊安全中心如何與 Azure Container Registry 搭配運作

以下是使用資訊安全中心保護登錄的所需元件和優點的高階圖表。

![Azure 資訊安全中心和 Azure Container Registry (ACR) 高階概觀](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure Container Registry 映像掃描的常見問題集

### <a name="how-does-security-center-scan-an-image"></a>資訊安全中心掃描映像的方式為何？
系統會從登錄中提取映像。 然後，映像會在隔離的沙箱中執行，並且由 Qualys 掃描器擷取已知弱點的清單。

資訊安全中心會篩選並分類掃描器發現的結果。 當映像狀況良好時，資訊安全中心會據以標示。 資訊安全中心只會針對有問題待解決的映像產生安全性建議。 資訊安全中心只會在發生問題時發出通知，藉以減少非必要的資訊警示。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>是否可透過 REST API 取得掃描結果？
是。 結果會存放在[子評量 Rest API](/rest/api/securitycenter/subassessments/list/) 下。 此外，您可以使用 Azure Resource Graph (ARG)，這是適用於所有資源的類 Kusto API：查詢可以擷取特定的掃描。

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>掃描的登錄類型為何？ 哪些類型需計費？
如需適用於容器登錄的 Azure Defender 所支援的容器登錄類型清單，請參閱[可用性](defender-for-container-registries-usage.md#availability)。

如果您將不支援的登錄連線至您的 Azure 訂用帳戶，則不會進行掃描，也不會向您收取費用。

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>我可以自訂來自弱點掃描器的結果嗎？
是。 如果您的組織需要忽略某個結果，而不是將其修復，您可以選擇性地停用該結果。 停用的結果不會影響您的安全分數或產生不想要的雜訊。

[深入了解建立規則以停用來自整合式弱點評估工具的結果](defender-for-container-registries-usage.md#disable-specific-findings-preview)。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [掃描映像的弱點](defender-for-container-registries-usage.md)