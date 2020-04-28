---
title: 服務層和 Sku
description: 深入瞭解 Azure Container Registry 的 Basic、Standard 和 Premium 服務層（Sku）中的功能和限制。
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456280"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) 具有多個服務層級 (稱為SKU)。 這些 SKU 會提供可預測的定價，以及數個可符合您在 Azure 之私用 Docker 登錄容量和使用模式的選項。

| SKU | 描述 |
| --- | ----------- |
| **基本** | 適用於正在學習 Azure Container Registry 之開發人員的成本最佳化進入點。 基本登錄具有與 Standard 和 Premium 相同的程式設計功能（例如 Azure Active Directory[驗證整合](container-registry-authentication.md#individual-login-with-azure-ad)、[映射刪除][container-registry-delete]和[webhook][container-registry-webhook]）。 不過，內含儲存體和映像輸送量最適合較低的使用方式情節。 |
| **Standard** | 「標準」登錄提供與「基本」相同的功能，並且提高內含儲存體和映像輸送量。 「標準」登錄應該能滿足大部分實際執行案例的需求。 |
| **Premium** | 「進階」登錄提供最多的內含儲存體和並行作業，可啟用大量情節。 除了更高的映射輸送量以外，Premium 還新增了[異地][container-registry-geo-replication]複寫等功能，可跨多個區域管理單一登入、影像標記簽署的[內容信任](container-registry-content-trust.md)、[防火牆和虛擬網路（預覽）](container-registry-vnet.md) ，以限制登錄的存取權。 |

Basic、Standard 和 Premium Sku 全都提供相同的程式設計功能。 它們也全都受益于 Azure 完全受控的[映射儲存體][container-registry-storage]。 選擇較高層級的 SKU 可提供更多的效能和延展性。 由於有多個服務層級，您可以一開始先使用「基本」，再隨著登錄使用量的增加而轉換為「標準」和「進階」。

## <a name="sku-features-and-limits"></a>SKU 功能和限制

下表詳述「基本」、「標準」和「進階」服務層級的功能和限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>變更 SKU

您可以使用 Azure CLI 或在 Azure 入口網站中變更登錄的 SKU。 只要您要切換的 SKU 具有所需的最大儲存容量，您就可以在 Sku 之間自由移動。 

### <a name="azure-cli"></a>Azure CLI

若要在 Azure CLI 中改用其他 SKU，請使用 [az acr update][az-acr-update] 命令。 例如，若要改用進階：

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站的容器登錄 [概觀]**** 中，選取 [更新]****，然後從 SKU 下拉式清單中選取新的 **SKU**。

![在 Azure 入口網站中更新容器登錄 SKU][update-registry-sku]

## <a name="pricing"></a>定價

如需每個 Azure Container Registry SKU 的定價資訊，請參閱 [Container Registry 定價][container-registry-pricing]。

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
