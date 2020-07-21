---
title: 登錄服務層和功能
description: 深入了解 Azure Container Registry 基本、標準和進階服務層級 (SKU) 中的功能和限制。
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 66cb5d7c3cdca45f7a44f0f23bfa449d5ade6dbf
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512602"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry 服務層級

Azure Container Registry 具有多個服務層級 (也稱為SKU)。 這些層級會提供可預測的定價，以及數個可符合您在 Azure 中私用 Docker 登錄容量和使用模式的選項。

| 層 | 描述 |
| --- | ----------- |
| **基本** | 適用於正在學習 Azure Container Registry 之開發人員的成本最佳化進入點。 「基本」登錄具有與「標準」和「進階」相同的程式設計功能 (例如 Azure Active Directory [驗證整合](container-registry-authentication.md#individual-login-with-azure-ad)、[映像刪除][container-registry-delete]和 [Webhook][container-registry-webhook])。 不過，內含儲存體和映像輸送量最適合較低的使用方式情節。 |
| **Standard** | 「標準」登錄提供與「基本」相同的功能，並且提高內含儲存體和映像輸送量。 「標準」登錄應該能滿足大部分實際執行案例的需求。 |
| **高級** | 「進階」登錄提供最多的內含儲存體和並行作業，可啟用大量情節。 除了更高的映像輸送量之外，「進階」也會新增功能，例如新增[異地複寫][container-registry-geo-replication]以在多個區域上管理單一登錄，針對映像標籤簽署新增[內容信任](container-registry-content-trust.md)，新增[具有私人端點的私人連結](container-registry-private-link.md)以限制登錄的存取權。 |

基本、標準和進階層級全都提供相同的程式設計功能。 也可從完全由 Azure 管理的[映像儲存體][container-registry-storage]中得到好處。 選擇較高的層級可提供更多的效能和延展性。 由於有多個服務層級，您可以一開始先使用「基本」，再隨著登錄使用量的增加而轉換為「標準」和「進階」。

## <a name="service-tier-features-and-limits"></a>服務層級功能和限制

下表詳細說明基本、標準和 Premium 服務層級的功能和登錄限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>變更層級

您可以使用 Azure CLI 或在 Azure 入口網站中變更登錄的服務層級。 您可以自由地改用其他層級，只要您所要改用的目標層級具有所需的最大儲存容量即可。 

### <a name="azure-cli"></a>Azure CLI

若要在 Azure CLI 中改用其他服務層級，請使用 [az acr update][az-acr-update] 命令。 例如，若要改用進階：

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站的容器登錄 [概觀] 中，選取 [更新]，然後從 SKU 下拉式清單中選取新的 **SKU**。

![在 Azure 入口網站中更新容器登錄 SKU][update-registry-sku]

## <a name="pricing"></a>定價

如需每個 Azure Container Registry 服務層級的定價資訊，請參閱[容器登錄定價][container-registry-pricing]。

如需資料傳輸定價的詳細資訊，請參閱[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。 

## <a name="next-steps"></a>後續步驟

**Azure Container Registry 藍圖**

請瀏覽 GitHub 上的 [ACR Roadmap][acr-roadmap] ACR 藍圖)，以尋找服務中即將推出功能的相關資訊。

**Azure Container Registry UserVoice**

送出並票選 [ACR UserVoice][container-registry-uservoice] 中的新功能建議。

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
