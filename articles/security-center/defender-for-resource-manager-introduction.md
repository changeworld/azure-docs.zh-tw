---
title: 適用於 Resource Manager 的 Azure Defender - 優點和功能
description: 了解適用於 Resource Manager 的 Azure Defender 有何優點和功能
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9591dae058a19cea73d88513b7c4ff4ab8f88045
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797741"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>適用於 Resource Manager 的 Azure Defender 簡介

[Azure Resource Manager](../azure-resource-manager/management/overview.md) 是 Azure 的部署和管理服務。 其提供管理層，可讓您建立、更新和刪除您 Azure 帳戶中的資源。 您可以使用存取控制、鎖定和標記等管理功能，在部署後保護及組織您的資源。

雲端管理層是連線到您所有雲端資源的重要服務。 因此，也是攻擊者的潛在目標。 有鑑於此，我們建議安全性作業小組應密切監視資源管理層。 

適用於 Resource Manager 的 Azure Defender 會自動監視您組織中的資源管理作業，不論是透過 Azure 入口網站、Azure REST API、Azure CLI 或其他 Azure 程式設計用戶端來管理皆適用。 Azure Defender 會執行進階的安全性分析來偵測威脅，並針對可疑的活動發出警示。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|預覽<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|定價：|**適用於Resource Manager 的 Azure Defender** 的計費方式如 [定價頁面](security-center-pricing.md)所示|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>適用於 Resource Manager 的 Azure Defender 有哪些優點？

適用於 Resource Manager 的 Azure Defender 可防範的問題包括：

- **可疑的資源管理作業**，例如來自可疑 IP 位址的作業、停用反惡意程式碼軟體及 VM 擴充功能中執行的可疑指令碼
- **使用惡意探索工具組**，例如 Microburst 或 PowerZure
- 從 Azure 管理層 **橫向移動** 至 Azure 資源資料平面

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure Resource Manager 概觀圖表":::

適用於 Resource Manager 的 Azure Defender 所提供的警示完整清單位於[警示參考頁面](alerts-reference.md#alerts-resourcemanager)。


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>如何從適用於 Resource Manager 的 Azure Defender 中調查警示

「適用於 Resource Manager 的 Azure Defender」是以監視 Azure Resource Manager 作業偵測到的威脅為依據，來發出安全性警示。 Azure Defender 會使用 Azure Resource Manager 的內部記錄來源和 Azure 活動記錄 (Azure 中的平台記錄，可讓您深入了解訂用帳戶層級的事件)。

深入了解 [Azure 活動記錄](../azure-monitor/platform/activity-log.md)。

若要從適用於 Resource Manager 的 Azure Defender 中調查安全性警示：

1. 開啟 Azure 活動記錄。

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="如何開啟 Azure 活動記錄":::

1. 將事件篩選為：
    - 警示中所述的訂用帳戶
    - 偵測的活動時間範圍
    - 相關的使用者帳戶 (如有相關)

1. 尋找可疑的活動。

> [!TIP]
> 如需更佳、更豐富的調查體驗，請將您的 Azure 活動記錄串流至 Azure Sentinel，如[從 Azure 活動記錄連結資料](../sentinel/connect-azure-activity.md)中所述。



## <a name="next-steps"></a>後續步驟

在本文中，您已了解適用於 Resource Manager 的 Azure Defender。 如需相關內容，請參閱下列文章： 

- 安全性警示可能由資訊安全中心產生，或由不同的安全性產品資訊安全中心接收。 若要將所有警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。

- > [!div class="nextstepaction"]
    > [啟用 Azure Defender](security-center-pricing.md#enable-azure-defender)