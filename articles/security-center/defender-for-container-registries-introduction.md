---
title: 適用于 container registry 的 Azure Defender-優點和功能
description: 瞭解適用于容器登錄的 Azure Defender 優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 6254b78ad19e7034f78f7891d57a3474fee0c602
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301914"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>適用于容器登錄的 Azure Defender 簡介

Azure Container Registry (ACR) 是受控的私人 Docker 登錄服務，可在中央登錄中儲存及管理 Azure 部署的容器映射。 它是以開放原始碼的 Docker Registry 2.0 為基礎。

若要保護訂用帳戶中所有以 Azure Resource Manager 為基礎的登錄，請在訂用帳戶層級啟用 **容器登錄的 Azure Defender** 。 然後，「安全性中心」會掃描推送至登錄、匯入登錄的映射，或在過去30天內提取的任何映射。 這項功能是依每個映射收費。

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>適用于 container registry 的 Azure Defender 有哪些優點？

「安全性中心」會識別您訂用帳戶中以 Azure Resource Manager 為基礎的 ACR 登錄，並針對您的登錄映射順暢地提供 Azure 原生弱點評定與管理。

**適用于 container registry 的 Azure Defender** 包含弱點掃描程式，可掃描您 Azure Resource Manager 型 Azure Container Registry 登錄中的映射，並提供更深入的映射弱點可見度。 整合式掃描器是由領先業界的弱點掃描廠商的 Qualys 提供技術支援。

當您發現問題時（透過 Qualys 或安全性中心），您將會在 [安全性中心] 儀表板中收到通知。 針對每個弱點，「安全性中心」提供可操作的建議，以及嚴重性分類，以及如何修復問題的指引。 如需資訊安全中心的容器建議詳細資訊，請參閱 [建議的參考清單](recommendations-reference.md#recs-containers)。

安全性中心會篩選並分類掃描器的結果。 當映射狀況良好時，「安全性中心」會將它標示為如此。 安全性中心只會針對有問題要解決的映射產生安全性建議。 資訊安全中心會提供每個報告的弱點和嚴重性分類的詳細資料。 此外，它也提供如何補救每個映射上所發現特定弱點的指引。

藉由只在發生問題時通知，資訊安全中心會降低不必要資訊警示的可能性。


## <a name="when-are-images-scanned"></a>影像掃描的時間為何？

影像掃描有三個觸發程式：

- **在推送** 時，只要將映射推送至您的登錄，資訊安全中心就會自動掃描該映射。 若要觸發映射掃描，請將其推送至您的存放庫。

- **最近的提取** -由於每日都會探索到新的弱點，因此 **Container Registry 的 Azure Defender** 也會掃描過去30天內已提取的任何映射。 重新掃描不需要額外收費;如前文所述，每個影像只會支付一次費用。

- **On import** -Azure Container Registry 具有匯入工具，可將映射從 Docker Hub、Microsoft Container registry 或其他 Azure container registry 帶入您的登錄。 **適用于 container registry 的 Azure Defender** 會掃描您匯入的任何支援映射。 深入瞭解 [如何將容器映射匯入至容器](../container-registry/container-registry-import-images.md)登錄。
 
掃描通常會在2分鐘內完成，但最多可能需要15分鐘的時間。 結果會以安全性中心建議的形式提供，如下列所示：

[![有關在 Azure Container Registry (ACR) 託管映射中探索到的弱點 Azure 資訊安全中心建議](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>安全性中心如何搭配 Azure Container Registry

以下是使用安全性中心保護您的登錄的元件和優點的高階圖表。

![Azure 資訊安全中心和 Azure Container Registry (ACR) 高階總覽](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure Container Registry 影像掃描的常見問題

### <a name="how-does-security-center-scan-an-image"></a>資訊安全中心如何掃描映射？
從登錄中提取映射。 然後，它會在隔離的沙箱中執行，並使用 Qualys 掃描器來解壓縮已知的弱點清單。

安全性中心會篩選並分類掃描器的結果。 當映射狀況良好時，「安全性中心」會將它標示為如此。 安全性中心只會針對有問題要解決的映射產生安全性建議。 藉由只在發生問題時通知，資訊安全中心會降低不必要資訊警示的可能性。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>我可以透過 REST API 取得掃描結果嗎？
可以。 結果會在 [子評量 REST API](/rest/api/securitycenter/subassessments/list/)下進行。 此外，您也可以使用 Azure Resource Graph (ARG) （適用于所有資源的 Kusto） API：查詢可以提取特定的掃描。
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>掃描的登錄類型為何？ 哪些類型會計費？
如需 Azure Defender 針對容器登錄所支援的容器登錄類型清單，請參閱 [可用性](defender-for-container-registries-usage.md#availability)。

如果您將不支援的登錄連接到您的 Azure 訂用帳戶，則不會進行掃描，而且不會向您收取費用。


## <a name="next-steps"></a>後續步驟

若要深入瞭解資訊安全中心的容器安全性功能，請參閱：

- [Azure 資訊安全中心與容器安全性](container-security.md)

- [Azure Defender for Kubernetes 簡介](defender-for-kubernetes-introduction.md)


