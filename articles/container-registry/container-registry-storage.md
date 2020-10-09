---
title: 容器映像儲存體
description: 有關您的 Docker 容器映像如何儲存在 Azure Container Registry 中的詳細資料，包括安全性、備援和容量。
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85214055"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry 中的容器映像儲存體

每個[基本、標準和進階](container-registry-skus.md) Azure 容器登錄都可以從進階 Azure 儲存體功能受益，例如適用於映像資料安全性的待用資料加密，以及適用於映像資料保護的異地備援。 下節描述 Azure Container Registry (ACR) 中映像儲存體的功能和限制。

## <a name="encryption-at-rest"></a>待用資料加密

您登錄中的所有容器映像在待用時都已加密。 Azure 會在儲存映像之前自動將它加密，並在您的應用程式或服務提取映像時將它解密。 選擇性地使用 [客戶管理的金鑰](container-registry-customer-managed-keys.md)來套用額外的加密層。

## <a name="geo-redundant-storage"></a>異地備援儲存體

Azure 使用異地備援儲存體配置來避免您的容器映像遺失。 Azure Container Registry 會自動將您的容器映像複寫到多個地理位置相距甚遠的資料中心，以避免它們在區域性的儲存體失敗時遺失。

## <a name="geo-replication"></a>異地複寫

針對需要更高可用性保證的案例，請考慮使用「進階」登錄的[異地複寫](container-registry-geo-replication.md)功能。 異地複寫有助於避免在「嚴重」的區域性失敗情況 (不僅是儲存體失敗) 中無法存取您的登錄。 異地複寫也提供其他好處，例如可在分散式開發或部署案例中加快推送和提取速度的網路鄰近映像儲存體。

## <a name="scalable-storage"></a>可擴充的存放裝置

Azure Container Registry 可讓您依需求建立多個存放庫、映射、圖層或標籤，最多可達登錄 [儲存空間限制](container-registry-skus.md#service-tier-features-and-limits)。 

大量的存放庫和標籤可能會影響您的登錄效能。 在登錄維護常式中定期刪除未使用的存放庫、標籤和映射，並選擇性地針對未標記的資訊清單設定 [保留原則](container-registry-retention-policy.md) 。 刪除後， *無法* 復原已刪除的登錄資源，例如存放庫、映射和標記。 如需刪除登錄資源的詳細資訊，請參閱[刪除 Azure Container Registry 中的容器映像](container-registry-delete.md)。

## <a name="storage-cost"></a>儲存成本

如需定價的完整詳細資訊，請參閱 [Azure Container Registry 定價][pricing]。

## <a name="next-steps"></a>後續步驟

如需基本、標準和進階容器登錄的詳細資訊，請參閱 [Azure Container Registry 服務層級](container-registry-skus.md)。

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
