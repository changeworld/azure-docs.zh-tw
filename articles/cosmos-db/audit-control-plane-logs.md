---
title: 如何稽核 Azure 宇宙 DB 控制平面操作
description: 瞭解如何稽核在 Azure Cosmos DB 中新增區域、更新輸送量、區域故障轉移、添加 VNet 等控制平面操作
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420263"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>如何稽核 Azure 宇宙 DB 控制平面操作

控制平面操作包括對 Azure Cosmos 帳戶或容器的更改。 例如,創建 Azure Cosmos 帳戶、添加區域、更新輸送量、區域故障轉移、添加 VNet 等是一些控制平面操作。 本文介紹如何審核 Azure Cosmos DB 中的控制平面操作。

## <a name="disable-key-based-metadata-write-access"></a>關閉基於金鑰的中繼資料寫入存取
 
在審核 Azure Cosmos DB 中的控制平面操作之前,請禁用帳戶上的基於金鑰的中數據寫入存取。 禁用基於密鑰的中繼資料寫入存取時,透過帳戶金鑰連接到 Azure Cosmos 帳戶的用戶端將被禁止存取該帳戶。 使用屬性設定為 true,`disableKeyBasedMetadataWriteAccess`可以關閉寫入存取。 設置此屬性後,任何資源的更改都可能僅從具有適當的基於角色的訪問控制 (RBAC) 角色和憑據的用戶進行。 要瞭解有關如何設置此屬性的更多詳細資訊,請參閱["防止 SDK 更改](role-based-access-control.md#preventing-changes-from-cosmos-sdk)"一文。

 關閉中繼資料寫入存取時請考慮以下幾點:

* 評估並確保應用程式不會使用 SDK 或帳戶金鑰進行更改上述資源的中繼資料調用(例如,創建集合、更新輸送量等)。

* 目前,Azure 門戶使用帳戶密鑰進行元數據操作,因此這些操作將被阻止。 或者,使用 Azure CLI、SDK 或資源管理器範本部署來執行此類操作。

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>為控制平面操作啟用診斷紀錄

可以使用 Azure 門戶為控制平面操作啟用診斷日誌。 使用以下步驟啟用控制平面操作上的紀錄記錄:

1. 登錄到[Azure 門戶](https://portal.azure.com)並導航到 Azure Cosmos 帳戶。

1. 開啟 **「診斷設定」** 窗格,為要建立的紀錄提供**名稱**。

1. 選擇**日誌類型的 ControlPlane 請求**,然後選擇「**發送到日誌分析**」選項。

您還可以將日誌存儲在存儲帳戶或流中存儲到事件中心。 本文演示如何發送日誌以進行日誌分析,然後查詢它們。 啟用後,診斷日誌需要幾分鐘才能生效。 可以跟蹤該點之後執行的所有控制平面操作。 以下螢幕擷取如何啟用控制平面紀錄:

![開啟控制平面要求紀錄記錄](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>檢視控制平面操作

開啟紀錄紀錄後,請使用以下步驟追蹤特定帳戶的操作:

1. 登入[Azure 門戶](https://portal.azure.com)。
1. 從左側導航打開 **「監視器」** 選項卡,然後選擇 **「日誌」** 窗格。 它打開一個 UI,您可以在其中輕鬆運行具有該特定帳戶的查詢。 執行以下查詢以檢視控制平面紀錄:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

將 VNET 新增到 Azure Cosmos 帳號時,以下螢幕截圖捕捉紀錄:

![新增 VNet 時控制平面紀錄](./media/audit-control-plane-logs/add-ip-filter-logs.png)

更新 Cassandra 表的輸送量時,以下螢幕截圖捕獲日誌:

![更新輸送量時控制平面紀錄](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>識別與特定作業關聯的識別

如果要進一步調試,可以使用活動 ID 或操作的時間戳標識**活動日誌**中的特定操作。 時間戳用於某些資源管理器用戶端,其中活動 ID 未顯式傳遞。 活動日誌提供有關啟動操作的標識的詳細資訊。 以下螢幕擷取如何使用活動 ID 並在活動紀錄中找到與其關聯的操作:

![使用活動識別碼並尋找操作](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>後續步驟

* [瀏覽 Azure 宇宙 DB 的 Azure 監視器](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [使用 Azure Cosmos DB 中的計量進行監視及偵錯](use-metrics.md)