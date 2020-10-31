---
title: 在 Azure Cosmos DB 中監視作業的輸送量使用量
description: 瞭解如何在 Azure Cosmos DB 中監視作業的輸送量或要求單位使用量。 Azure Cosmos DB 帳戶的擁有者可以瞭解哪些作業需要更多的要求單位。
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: d6f0f30d7437b6f8ecc1d915eb3d3195f2504fec
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098241"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>如何在 Azure Cosmos DB 中監視作業的輸送量或要求單位使用量
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 的 Azure 監視器會提供計量視圖，以監視您的帳戶和建立儀表板。 預設會收集 Azure Cosmos DB 計量，這項功能不會要求您明確啟用或設定任何設定。 **要求單位計量總計** 用來取得不同類型作業的要求單位使用量。 您稍後可以分析哪些作業使用大部分的輸送量。 依預設，輸送量資料會以一分鐘的間隔匯總。 不過，您可以藉由變更時間細微性選項來變更摘要單位。

有兩種方式可以分析要求單位的使用量資料：

* 在指定的時間間隔內，哪些作業會花費更多要求單位。
* 一般哪些作業會耗用更多的要求單位，以將您的工作負載占到哪些。
這種分析可讓您專注于像是 insert、upsert 和查看其索引等作業。 您可以找出您是否在編制特定欄位的索引，並修改 [索引編制原則](index-policy.md#include-exclude-paths) 以包含或排除路徑。

如果您注意到某些查詢需要更多要求單位，您可以採取下列動作：

* 請重新考慮您是否要求正確的資料量。
* 修改查詢以使用 index with filter 子句。
* 執行成本較低的 UDF 函式呼叫。
* 定義分割區索引鍵，以最小化從查詢移出不同分割區的次數。
* 您也可以使用呼叫回應中傳回的查詢度量、診斷記錄詳細資料，並參閱 [查詢效能微調](sql-api-query-metrics.md) 文章，以深入瞭解查詢執行。
* 您可以從 sum 開始，然後查看使用正確維度的平均使用率。

## <a name="view-the-total-request-unit-usage-metric"></a>查看總要求單位使用量度量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導覽列中選取 [ **監視** ]，然後選取 [ **計量** ]。

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Azure 監視器中的 [計量] 窗格":::

1. 從 [計量] 窗格 > **選取資源** > 選擇必要的 **訂用帳戶** 和 **資源群組** 。 在 [資源類型] 中，選取 [Azure Cosmos DB 帳戶]，然後選擇其中一個現有的 Azure Cosmos 帳戶並選取 [套用]。

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="Azure 監視器中的 [計量] 窗格":::

1. 接下來，從可用計量清單中選取 [ **要求單位總計** ] 計量。 若要深入了解此清單中所有可用的計量，請參閱[依類別區分的計量](monitor-cosmos-db-reference.md)一文。 在此範例中，讓我們選取 **總要求單位** 和 **Avg** 作為匯總值。 除了這些詳細資料之外，您也可以選取計量的 [時間範圍] 和 [時間細微性]。 在 [最大值] 中，您可以檢視過去 30 天的計量。  套用篩選之後，圖表就會根據您的篩選條件來顯示。 您可以看到所選期間內每分鐘耗用的平均要求單位數。  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Azure 監視器中的 [計量] 窗格":::

## <a name="filters-for-request-unit-usage"></a>要求單位使用方式的篩選

您也可以篩選計量，並取得特定 **CollectionName** 、 **DatabaseName** 、 **OperationType** 、 **Region** 、 **Status** 和 **StatusCode** 所顯示的圖表。 [ **加入篩選** ] 和 [套用 **分割** ] 選項可讓您篩選要求單位使用方式，並將計量分組。

若要取得每項作業的要求單位使用量（依 total (sum) 或 average），請選取 [套用 **分割** ]，然後選擇 [作業 **類型** ] 和 [篩選] 值，如下圖所示：

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Azure 監視器中的 [計量] 窗格":::

如果您想要依集合查看要求單位使用量，請選取 [套用 **分割** ]，然後選擇集合名稱做為篩選準則。 您會看到類似下列的聊天，以及儀表板內的集合選擇。 然後，您可以選取特定的集合名稱來查看更多詳細資料：

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Azure 監視器中的 [計量] 窗格":::

## <a name="next-steps"></a>下一步

* 使用 Azure 中的 [診斷設定](cosmosdb-monitor-resource-logs.md) 來監視 Azure Cosmos DB 資料。
* [Audit Azure Cosmos DB 控制平面作業](audit-control-plane-logs.md)
