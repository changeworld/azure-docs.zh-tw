---
title: 在 Azure Cosmos DB 中監視作業的輸送量使用量
description: 瞭解如何在 Azure Cosmos DB 中監視作業的輸送量或要求單位使用量。 Azure Cosmos DB 帳戶的擁有者可以瞭解哪些作業會採用更多的要求單位。
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: e3ec3bc0e0c6028eb5d7a90ee1920938d792941b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260741"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>如何在 Azure Cosmos DB 中監視作業的輸送量或要求單位使用量

Azure Cosmos DB 的 Azure 監視器會提供計量視圖來監視您的帳戶並建立儀表板。 預設會收集 Azure Cosmos DB 計量，這項功能不會要求您明確啟用或設定任何專案。 「**要求單位**」的總度量是用來取得不同作業類型的要求單位使用量。 稍後您可以分析最常使用哪些輸送量的作業。 根據預設，輸送量資料會以一分鐘的間隔匯總。 不過，您可以藉由變更 [時間細微性] 選項來變更摘要單位。

有兩種方式可以分析要求單位使用量資料：

* 在指定的時間間隔內，哪些作業接受較多的要求單位。
* 哪些作業一般會耗用更多的要求單位，而使您的工作負載更加分散。
這種分析可讓您專注于插入、upsert 和查看索引等作業。 您可以找出您是否超過或低於索引特定欄位，並修改[索引編制原則](index-policy.md#include-exclude-paths)以包含或排除路徑。

如果您注意到某些查詢會佔用更多的要求單位，您可以採取下列動作：

* 請重新考慮您是否要求正確的資料量。
* 修改查詢以搭配使用索引與篩選子句。
* 執行較不昂貴的 UDF 函式呼叫。
* 定義分割區索引鍵，將查詢的磁區降到不同的分割區。
* 您也可以使用呼叫回應中所傳回的查詢計量、診斷記錄詳細資料，並參閱[查詢效能微調](sql-api-query-metrics.md)一文，以深入瞭解查詢執行。
* 您可以從 [總和] 開始，然後使用正確的維度查看 [平均使用率]。

## <a name="view-the-total-request-unit-usage-metric"></a>查看總要求單位使用量度量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導覽列選取 [**監視**]，然後選取 [**計量**]。

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Azure 監視器中的 [計量] 窗格":::

1. 從 [計量] 窗格 > **選取資源** > 選擇必要的**訂用帳戶**和**資源群組**。 在 [資源類型] 中，選取 [Azure Cosmos DB 帳戶]，然後選擇其中一個現有的 Azure Cosmos 帳戶並選取 [套用]。

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="選擇要用來查看計量的 Azure Cosmos DB 帳戶":::

1. 接下來，從可用計量清單中選取 [**要求單位總數**] 度量。 若要深入了解此清單中所有可用的計量，請參閱[依類別區分的計量](monitor-cosmos-db-reference.md)一文。 在此範例中，讓我們選取 [**要求單位總數**] 和 [**平均**] 做為匯總值。 除了這些詳細資料之外，您也可以選取計量的 [時間範圍] 和 [時間細微性]。 在 [最大值] 中，您可以檢視過去 30 天的計量。  套用篩選之後，圖表就會根據您的篩選條件來顯示。 您可以看到所選期間內每分鐘耗用的平均要求單位數。  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="從 Azure 入口網站選擇計量":::

## <a name="filters-for-request-unit-usage"></a>要求單位使用量的篩選器

您也可以篩選計量，並取得特定**CollectionName**、 **DatabaseName**、 **OperationType**、 **Region**、 **Status**和**StatusCode**所顯示的圖表。 [**加入篩選**] 和 [套用**分割**] 選項可讓您篩選要求單位的使用量，並將計量分組。

若要依總計（總和）或平均取得每項作業的要求單位使用方式，請選取 [套用**分割**]，然後選擇 [作業**類型**] 和篩選值，如下圖所示：

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="在 Azure 監視器中 Cosmos DB 作業要求單位":::

如果您想要依集合查看要求單位的使用量，請選取 [套用**分割**]，然後選擇集合名稱作為篩選準則。 您會看到類似下列的交談，並在儀表板內選擇集合。 然後，您可以選取特定的集合名稱以查看更多詳細資料：

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Azure 監視器中的集合所有作業的 Cosmos DB 要求單位":::

## <a name="next-steps"></a>後續步驟

* 使用 Azure 中的[診斷設定](cosmosdb-monitor-resource-logs.md)來監視 Azure Cosmos DB 資料。
* [Audit Azure Cosmos DB 控制平面作業](audit-control-plane-logs.md)
