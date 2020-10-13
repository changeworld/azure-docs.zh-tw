---
title: 如何在 Azure Cosmos DB 中監視作業的伺服器端延遲
description: 瞭解如何在 Azure Cosmos DB 帳戶或容器中監視作業的伺服器延遲。 Azure Cosmos DB 帳戶的擁有者可以瞭解 Azure Cosmos 帳戶的伺服器端延遲問題。
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 9c266e42804a12403e446bf024e93fe879497570
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803257"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>如何監視 Azure Cosmos DB 容器或帳戶中作業的伺服器端延遲

Azure Cosmos DB 的 Azure 監視器會提供計量視圖，以監視您的帳戶和建立儀表板。 預設會收集 Azure Cosmos DB 計量，這項功能不會要求您明確啟用或設定任何設定。 伺服器端延遲標準可用來查看作業的伺服器端延遲。 Azure Cosmos DB 為具有直接連線能力的點讀/寫作業提供小於10毫秒的 SLA。 針對點讀取和寫入作業，sla 會依照 [sla 檔](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)中的詳細說明進行計算。

如果您看到點作業的異常高延遲（例如：

* 在直接連線模式中具有分割區索引鍵和識別碼的 GET 或 SET 作業
* 讀取或寫入作業或
* 查詢

您可以查閱診斷記錄，以查看傳回的資料大小。 如果您發現查詢作業有持續的高延遲，您應該查閱診斷記錄，以取得更高的 [輸送量或使用的 RU/秒](cosmosdb-monitor-resource-logs.md#diagnostic-queries) 。 伺服器端延遲會顯示資料傳回給用戶端之前，在後端基礎結構上花費的時間量。 請務必查看此度量，以排除任何後端延遲問題。

## <a name="view-the-server-side-latency-metric"></a>查看伺服器端延遲標準

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導覽列中選取 [ **監視** ]，然後選取 [ **計量**]。

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Azure 監視器中的 [計量] 窗格":::

1. 從 [計量] 窗格 > **選取資源** > 選擇必要的**訂用帳戶**和**資源群組**。 在 [資源類型] 中，選取 [Azure Cosmos DB 帳戶]，然後選擇其中一個現有的 Azure Cosmos 帳戶並選取 [套用]。
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Azure 監視器中的 [計量] 窗格":::

1. 接下來，從可用計量清單中選取 **伺服器端延遲**  標準。 若要深入了解此清單中所有可用的計量，請參閱[依類別區分的計量](monitor-cosmos-db-reference.md)一文。 在此範例中，讓我們選取 [ **伺服器端延遲** ] 和 [ **Avg** ] 作為匯總值。 除了這些詳細資料之外，您也可以選取計量的 [時間範圍] 和 [時間細微性]。 在 [最大值] 中，您可以檢視過去 30 天的計量。  套用篩選之後，圖表就會根據您的篩選條件來顯示。 您可以查看所選期間的伺服器端每分鐘延遲。  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Azure 監視器中的 [計量] 窗格":::

## <a name="filters-for-server-side-latency"></a>伺服器端延遲的篩選

您也可以篩選計量，並取得特定 **CollectionName**、 **ConnectionMode**、 **DatabaseName**、 **OperationType**、 **Region**和 **PublicAPIType**所顯示的圖表。 

若要篩選計量，請選取 [ **加入篩選** ] 並選擇必要的屬性，例如 **PublicAPIType** ，然後選取 [ **sql**] 值。 新增 **OperationType**的另一個篩選準則。 然後，圖形會顯示所選期間內不同作業的伺服器端延遲。 透過預存程序執行的作業並不會記錄，因此其無法在 OperationType 計量下提供。

每項作業的 **伺服器端延遲** 計量都會顯示如下圖所示：

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Azure 監視器中的 [計量] 窗格":::

您也可以使用 [套用 **分割** ] 選項將計量分組。  

## <a name="next-steps"></a>後續步驟

* 使用 Azure 中的 [診斷設定](cosmosdb-monitor-resource-logs.md) 來監視 Azure Cosmos DB 資料。
* [Audit Azure Cosmos DB 控制平面作業](audit-control-plane-logs.md)
