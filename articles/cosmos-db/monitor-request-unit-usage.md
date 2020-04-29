---
title: 在 Azure Cosmos DB 中監視作業的輸送量使用量
description: 瞭解如何在 Azure Cosmos DB 中監視作業的輸送量或要求單位使用量。 Azure Cosmos DB 帳戶的擁有者可以瞭解哪些作業會採用更多的要求單位。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81115426"
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

   ![Azure 監視器中的 [計量] 窗格](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. 從 [**計量**] 窗格 >**選取資源**> 選擇所需的**訂**用帳戶和**資源群組**。 針對 [**資源類型**]，選取 [ **Azure Cosmos DB 帳戶**] **，選擇其中**一個現有的 Azure Cosmos 帳戶，然後選取 [套用]。

   ![選擇要用來查看計量的 Azure Cosmos DB 帳戶](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. 接下來，從可用計量清單中選取 [**要求單位總數**] 度量。 若要深入瞭解此清單中所有可用的計量，請參閱[依類別](monitor-cosmos-db-reference.md)區分的計量一文。 在此範例中，讓我們選取 [**要求單位總數**] 和 [**平均**] 做為匯總值。 除了這些詳細資料之外，您也可以選取度量的**時間範圍**和**時間細微性**。 在 [最大值] 中，您可以查看過去30天的計量。  套用篩選之後，就會根據您的篩選器來顯示圖表。 您可以看到所選期間內每分鐘耗用的平均要求單位數。  

   ![從 Azure 入口網站選擇度量](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>要求單位使用量的篩選器

您也可以篩選計量，並取得特定**CollectionName**、 **DatabaseName**、 **OperationType**、 **Region**、 **Status**和**StatusCode**所顯示的圖表。 [**加入篩選**] 和 [套用**分割**] 選項可讓您篩選要求單位的使用量，並將計量分組。

若要依總計（總和）或平均取得每項作業的要求單位使用方式，請選取 [套用**分割**]，然後選擇 [作業**類型**] 和篩選值，如下圖所示：

   ![在 Azure 監視器中 Cosmos DB 作業要求單位](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

如果您想要依集合查看要求單位的使用量，請選取 [套用**分割**]，然後選擇集合名稱作為篩選準則。 您會看到類似下列的交談，並在儀表板內選擇集合。 然後，您可以選取特定的集合名稱以查看更多詳細資料：

   ![Azure 監視器中的集合所有作業的 Cosmos DB 要求單位](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>後續步驟

* 使用 Azure 中的[診斷設定](cosmosdb-monitor-resource-logs.md)來監視 Azure Cosmos DB 資料。
* [Audit Azure Cosmos DB 控制平面作業](audit-control-plane-logs.md)