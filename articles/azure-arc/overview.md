---
title: Azure Arc 概觀
description: 了解什麼是 Azure Arc，以及其如何協助客戶使用其他 Azure 服務和功能來管理及治理其混合式資源。
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 8f39dff16037b5f52a050b7d07193b0ad7cab93d
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672681"
---
# <a name="azure-arc-overview"></a>Azure Arc 概觀

現今，許多公司都致力於控制和治理越來越複雜的環境。 這些環境跨越了資料中心、多個雲端和邊緣。 每個環境和雲端都自己有一組獨立的管理工具，您需要學習和操作。

與此同時，新的 DevOps 和 ITOps 作業模型難以實行，因為現有的工具無法為新的雲端原生模式提供支援。

Azure Arc 可提供一致的多雲端和內部部署管理平台來簡化治理和管理。 Azure Arc 可讓您將現有的資源投射到 Azure Resource Manager，透過單一窗口來管理整個環境。 您現在可以管理虛擬機器、Kubernetes 叢集和資料庫，就像這些服務都在 Azure 中執行一樣。 無論其位於何處，您都可以使用熟悉的 Azure 服務和管理功能。 Azure Arc 可讓您繼續使用傳統 ITOps，同時引進 DevOps 實務來支援您環境中的新雲端原生模式。

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Azure Arc 管理控制平面圖表" border="false":::

現在，Azure Arc 可讓您管理 Azure 外部託管的下列資源類型：

* 伺服器 - 執行 Windows 或 Linux 的實體和虛擬機器。
* Kubernetes 叢集 - 支援多個 Kubernetes 分佈。
* Azure 資料服務 - Azure SQL Database 和 PostgreSQL 超大規模資料庫服務。

## <a name="what-does-azure-arc-deliver"></a>Azure Arc 提供什麼功能？

Azure Arc 的主要功能包括：

* 為您環境中的伺服器實作一致的清查、管理、治理和安全性。

* 設定 [Azure VM 擴充功能](./servers/manage-vm-extensions.md)，以使用 Azure 管理服務來監視、保護及更新您的伺服器。

* 大規模管理和治理 Kubernetes 叢集。

* 使用以 GitOps 為基礎的設定作為程式碼管理方式，直接從原始檔控制 (例如 GitHub) 在一個或多個叢集上部署應用程式和設定。

* 使用 Azure 原則，以零接觸的方式完成 Kubernetes 叢集的合規性和設定。

* 在任何 Kubernetes 環境 (特別是 Azure SQL 受控執行個體和適用於 PostgreSQL 超大規模資料庫的 Azure 資料庫) 上執行 Azure 資料服務，其優點包括升級/更新、安全性和監視，如同在 Azure 中執行一樣。 運用彈性擴縮、套用更新、不需要停止任何應用程式 (即使沒有與 Azure 持續連線)。

* 無論您使用的是 Azure 入口網站、Azure CLI、Azure PowerShell 或 Azure REST API，都能以一致的方式來檢視已啟用 Azure Arc 的資源。

## <a name="how-much-does-azure-arc-cost"></a>Azure Arc 的費用為何？

以下是 Azure Arc 目前可用功能的定價詳細資料。

### <a name="arc-enabled-servers"></a>已啟用 Arc 的伺服器

使用 Azure Arc 控制平面功能不需額外付費。 這包括：

* 透過 Azure 管理群組和標籤的資源組織。

* 透過 Azure Resource Graph 進行搜尋和索引編製。

* 透過 Azure RBAC 及訂用帳戶的存取權及安全性。

* 透過範本及延伸模組的環境及自動化。

* 更新管理

在啟用 Arc 的伺服器上使用任何 Azure 服務 (例如 Azure 資訊安全中心或 Azure 監視器)，則會依據該服務的定價收費。 如需詳細資訊，請參閱 [Azure 定價頁面](https://azure.microsoft.com/pricing/)。

### <a name="azure-arc-enabled-kubernetes"></a>已啟用 Azure Arc 的 Kubernetes

在目前的預覽階段中，啟用 Azure Arc 的 Kubernetes 不需額外付費。

### <a name="azure-arc-enabled-data-services"></a>已啟用 Azure Arc 的資料服務

在目前的預覽階段中，啟用 Azure Arc 的資料服務不需額外付費。

## <a name="next-steps"></a>後續步驟

* 若要深入了解啟用 Arc 的伺服器，請參閱下列[概觀](./servers/overview.md)

* 若要深入了解啟用 Arc 的 Kubernetes，請參閱下列[概觀](./kubernetes/overview.md)

* 若要深入了解啟用 Arc 的資料服務，請參閱下列[概觀](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)

* 從[概念證明開始](https://azurearcjumpstart.io/azure_arc_jumpstart/)，體驗已啟用 Arc 的服務
