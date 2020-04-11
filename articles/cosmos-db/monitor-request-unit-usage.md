---
title: 監視 Azure Cosmos DB 中動作的輸送量使用方式
description: 瞭解如何監視 Azure Cosmos DB 中操作的輸送量或請求單位使用方式。 Azure Cosmos DB 帳戶的擁有者可以瞭解哪些操作佔用了更多請求單元。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115426"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>如何監控 Azure Cosmos DB 中操作的輸送量或請求單位使用方式

Azure Cosmos DB 的 Azure 監視器提供了一個指標檢視,用於監視您的帳戶並創建儀表板。 默認情況下,Azure Cosmos DB 指標是收集的,此功能不需要您顯式啟用或配置任何內容。 **總請求單位**指標用於獲取不同類型的操作的請求單位使用方式。 稍後,您可以分析哪些操作使用了大部分輸送量。 默認情況下,輸送量數據以一分鐘間隔聚合。 但是,您可以通過更改時間粒度選項來更改聚合單元。

有兩種方法可以分析請求單位使用方式資料:

* 在給定的時間間隔內,哪些操作佔用了更多的請求單位。
* 哪些操作通常通過消耗更多請求單位來主導您的工作負載。
此分析允許您專注於插入、向上計算等操作,並查看其索引。 您可以找出是否過度/低於索引特定欄位,並修改[索引策略](index-policy.md#include-exclude-paths)以包括或排除路徑。

如果您注意到某些查詢正在獲取更多請求單位,則可以採取如下操作:

* 如果您請求了適當數量的數據,請重新考慮。
* 修改查詢以使用索引與篩選器子句。
* 執行成本較低的UDF函數呼叫。
* 定義分區鍵,以盡量減少風扇從查詢到不同的分區。
* 您還可以使用呼叫回應中返回的查詢指標、診斷日誌詳細資訊,並參考[查詢性能調優](sql-api-query-metrics.md)一文來瞭解有關查詢執行的詳細資訊。
* 可以從總和開始,然後使用正確的維度查看平均利用率。

## <a name="view-the-total-request-unit-usage-metric"></a>檢視總請求單位使用情況指標

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導航列中選擇 **「監視器」 然後**選擇 **「指標**」。

   ![Azure 監視器中的指標窗格](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. 從 **「指標」** 窗格>**選擇資源**>选择所需的**訂閱**與資源**群組**。 對於**資源類型**,選擇**Azure 宇宙資料庫帳戶**,選擇現有 Azure Cosmos 帳戶之一,然後選擇 **"應用**"。

   ![選擇 Azure 宇宙資料庫帳戶以檢視指標](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. 接下來,從可用指標清單中選擇**總請求單位**指標。 要詳細瞭解此清單中的所有可用指標,請參閱[按類別的指標](monitor-cosmos-db-reference.md)。 在此示例中,讓我們選擇**總請求單位**,選擇**平均值**作為聚合值。 除了這些詳細資訊之外,您還可以選擇指標**的時間範圍**和**時間粒度**。 最多時,您可以查看過去 30 天的指標。  應用篩選器後,會根據您的篩選器顯示圖表。 您可以看到所選期間每分鐘消耗的請求單位的平均數量。  

   ![從 Azure 門戶選擇指標](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>要求單位使用方式的篩選器

您還可以篩選指標,並獲取由特定**集合名稱**、**資料庫名稱**、**操作類型**、**區域**、**狀態**和**狀態代碼**顯示的圖表。 '**新增'篩選器**和 **「應用程式分割」** 選項允許您篩選請求單位使用方式並分組指標。

要按總計(總和)或平均值獲取每個操作的請求單位使用方式,請選擇 **「應用分割**」並選擇**操作類型**和篩選器值,如下圖所示:

   ![宇宙 DB 請求單位 以在 Azure 監視器中操作](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

如果要按集合查看請求單位使用方式,請選擇 **「應用拆分**」,然後選擇集合名稱作為篩選器。 您將看到如下所示的聊天,並在儀錶板中選擇集合。 然後,您可以選擇特定的集合名稱以查看更多詳細資訊:

   ![宇宙 DB 請求 Azure 監視器中集合的所有操作的單位](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>後續步驟

* 使用 Azure 中的[診斷設置](cosmosdb-monitor-resource-logs.md)監視 Azure 宇宙資料庫數據。
* [稽核 Azure 宇宙 DB 控制平面操作](audit-control-plane-logs.md)