---
title: 如何在 Azure Cosmos DB 中監視作業的伺服器端延遲
description: 瞭解如何在 Azure Cosmos DB 帳戶或容器中監視作業的伺服器延遲。 Azure Cosmos DB 帳戶的擁有者可以瞭解 Azure Cosmos 帳戶的伺服器端延遲問題。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 0f1e6d07afb3b7b4d26081bc9e34ac257b280d0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113913"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>如何監視 Azure Cosmos DB 容器或帳戶中作業的伺服器端延遲

Azure Cosmos DB 的 Azure 監視器會提供計量視圖來監視您的帳戶並建立儀表板。 預設會收集 Azure Cosmos DB 計量，這項功能不會要求您明確啟用或設定任何專案。 伺服器端的延遲計量是用來查看作業的伺服器端延遲。 Azure Cosmos DB 針對具有直接連線能力的點讀取/寫入作業提供少於10毫秒的 SLA。 針對點讀取和寫入作業，sla 的計算方式如[sla 檔](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)中所述。

如果您看到點作業異常的大型延遲，例如：

* 具有直接模式之分割區索引鍵和識別碼的取得或設定作業
* 讀取或寫入作業，或
* 查詢

您可以查閱診斷記錄，以查看傳回的資料大小。 如果您看到查詢作業有持續的高延遲，您可以查閱診斷記錄中所傳回的資料大小、[輸送量或使用的 RU/秒](cosmosdb-monitor-resource-logs.md#diagnostic-queries)，或在指定期間內的這類作業數目。 如此一來，您就可以在伺服器端的延遲問題中進行調試。

## <a name="view-the-server-side-latency-metric"></a>查看伺服器端延遲標準

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導覽列選取 [**監視**]，然後選取 [**計量**]。

   ![Azure 監視器中的 [計量] 窗格](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. 從 [**計量**] 窗格 >**選取資源**> 選擇所需的**訂**用帳戶和**資源群組**。 針對 [**資源類型**]，選取 [ **Azure Cosmos DB 帳戶**] **，選擇其中**一個現有的 Azure Cosmos 帳戶，然後選取 [套用]。
   
   ![選擇要用來查看計量的 Azure Cosmos DB 帳戶](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. 接下來，從可用計量清單中選取 [**伺服器端延遲**] 度量。 若要深入瞭解此清單中所有可用的計量，請參閱[依類別](monitor-cosmos-db-reference.md)區分的計量一文。 在此範例中，讓我們選取 [**伺服器端延遲**] 和 [**平均**] 做為匯總值。 除了這些詳細資料之外，您也可以選取度量的**時間範圍**和**時間細微性**。 在 [最大值] 中，您可以查看過去30天的計量。  套用篩選之後，就會根據您的篩選器來顯示圖表。 您可以看到所選期間內每分鐘的伺服器端延遲。  

   ![從 [Azure 入口網站中選擇伺服器端的延遲標準](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>伺服器端延遲的篩選

您也可以篩選計量，並取得特定**CollectionName**、 **ConnectionMode**、 **DatabaseName**、 **OperationType**、 **Region**和**PublicAPIType**所顯示的圖表。 

若要篩選計量，請選取 [**新增篩選**] 並選擇必要的屬性（例如**PublicAPIType** ），然後選取 [ **sql**] 值。 為**OperationType**新增另一個篩選準則。 然後圖表會顯示所選期間內不同作業的伺服器端延遲。 不會記錄透過預存程式執行的作業，因此它們無法在 OperationType 度量下提供。

下圖顯示每個作業的**伺服器端延遲**計量：

![伺服器端延遲計量的篩選](./media/monitor-server-side-latency/server-side-latency-filters.png)

您也可以使用 [套用**分割**] 選項將計量分組。  

## <a name="next-steps"></a>後續步驟

* 使用 Azure 中的[診斷設定](cosmosdb-monitor-resource-logs.md)來監視 Azure Cosmos DB 資料。
* [Audit Azure Cosmos DB 控制平面作業](audit-control-plane-logs.md)