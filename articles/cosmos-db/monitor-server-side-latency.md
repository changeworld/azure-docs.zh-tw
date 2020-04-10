---
title: 如何監控 Azure Cosmos DB 中操作的伺服器端延遲
description: 瞭解如何監視 Azure Cosmos DB 帳戶或容器中操作的操作的伺服器延遲。 Azure Cosmos DB 帳戶的擁有者可以瞭解 Azure Cosmos 帳戶的伺服器端延遲問題。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 62c10a2ada9ff7d3bf7090028dd9684192517d02
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991382"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>如何監控 Azure Cosmos DB 容器或帳戶中操作的伺服器端延遲

Azure Cosmos DB 的 Azure 監視器提供了一個指標檢視,用於監視您的帳戶並創建儀表板。 默認情況下,Azure Cosmos DB 指標是收集的,此功能不需要您顯式啟用或配置任何內容。 伺服器端延遲指標用於查看操作的伺服器端延遲。 Azure Cosmos DB 為具有直接連接的點讀取/寫入操作提供小於 10 毫秒的 SLA。 對於點讀取和寫入操作,SLA 的計算方式如下[SLA 文檔中](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)的詳細資訊。

如果看到點操作的異常大延遲,例如:

* 直接模式下具有分割區鍵與 ID 的取得或設定操作
* 讀寫作業或
* 查詢

您可以尋找診斷日誌以查看返回的數據大小。 如果看到查詢操作的持續高延遲,則可以查找診斷日誌,瞭解返回的數據大小、使用的[輸送量或 RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries)大小,或給定時間段內此類操作的數量。 這樣,您可以調試伺服器端延遲問題。

## <a name="view-server-side-latency-metric"></a>檢視伺服器端延遲指標

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導航列中選擇 **「監視器」 然後**選擇 **「指標**」。

   ![Azure 監視器中的指標窗格](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. 從 **「指標」** 窗格>**選擇資源**>选择所需的**訂閱**與資源**群組**。 對於**資源類型**,選擇**Azure 宇宙資料庫帳戶**,選擇現有 Azure Cosmos 帳戶之一,然後選擇 **"應用**"。
   
   ![選擇 Cosmos DB 帳號以檢視指標](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. 接下來,從可用指標清單中選擇**伺服器端延遲**指標。 要詳細瞭解此清單中的所有可用指標,請參閱[按類別的指標](monitor-cosmos-db-reference.md)。 在此範例中,讓我們選擇**伺服器端延遲**和**Avg**作為聚合值。 除了這些詳細資訊之外,您還可以選擇指標**的時間範圍**和**時間粒度**。 最多時,您可以查看過去 30 天的指標。  應用篩選器後,會根據您的篩選器顯示圖表。 您可以看到所選期間每分鐘消耗的請求單位的平均數量。  

   ![從 Azure 門戶選擇伺服器端延遲指標](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>伺服器端延遲的篩選器

您還可以篩選指標和圖表顯示由特定的**集合名稱**、**連接模式**、**資料庫名稱**、**操作類型**、**區域**和**公共API類型**顯示。 

要篩選指標,請選擇 **「新增篩選器」** 並選擇所需的屬性(如**PublicAPIType)** 並選擇值**sql**。 為**操作類型**添加另一個篩選器。 然後,該圖顯示所選時間段內不同操作的伺服器端延遲。 不會記錄通過存儲過程執行的操作,因此在操作類型指標下它們不可用。

每個操作的**伺服器端延遲**指標如下所示:

![伺服器端延遲指標的篩選器](./media/monitor-server-side-latency/server-side-latency-filters.png)

您還可以使用 **「應用分割**」選項對指標進行分組。  

## <a name="next-steps"></a>後續步驟

* 使用 Azure 的[診斷設定](cosmosdb-monitor-resource-logs.md)監視 Azure 宇宙資料庫資料
* [稽核 Azure 宇宙 DB 控制平面操作](audit-control-plane-logs.md)