---
title: 監視 Azure Cosmos 容器或帳戶的正規化 RU/秒
description: 瞭解如何在 Azure Cosmos DB 中監視作業的正規化要求單位使用狀況。 Azure Cosmos DB 帳戶的擁有者可以瞭解哪些作業耗用較多的要求單位。
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 8709389208ba1320685b1834b20893f08ef33ed7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482899"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>如何監視 Azure Cosmos 容器或帳戶的正規化 RU/秒

Azure Cosmos DB 的 Azure 監視器會提供計量視圖來監視您的帳戶並建立儀表板。 預設會收集 Azure Cosmos DB 計量，這項功能不會要求您明確啟用或設定任何專案。

**正規化的 RU 耗用量**計量可用來查看複本對於跨分割區索引鍵範圍的要求單位耗用量的飽和程度。 Azure Cosmos DB 會將輸送量平均分散到所有實體分割區。 此計量提供複本集內最大輸送量使用率的每秒查看量。 使用此計量來計算給定容器的分割區之間的 RU/秒使用量。 藉由使用此計量，如果您看到要求單位使用率過高的百分比，您應該增加輸送量以符合工作負載的需求。

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>當正規化 RU/秒較高時所預期的情況

當正規化的 RU/秒耗用量達到100% 時，用戶端會收到速率限制錯誤。 用戶端應該遵守等待時間並重試。 如果達到100% 使用率的短暫尖峰，表示複本上的輸送量已達到其最大效能限制。 例如，在複本上取用所有 RU/秒的預存程式之類的單一作業，將會導致正規化 RU/秒耗用量的短暫尖峰。 在這種情況下，如果要求速率很低，就不會有任何立即的速率限制錯誤。 這是因為，Azure Cosmos DB 可讓要求針對特定要求收取超過布建的 RU/秒，而且該時段內的其他要求會受到速率限制。

Azure 監視器計量可協助您使用 [**要求總數**] 度量來尋找每個狀態碼的作業。 稍後您可以根據429狀態碼來篩選這些要求，並依作業**類型**進行分割。

若要尋找速率受限的要求，建議的方法是透過診斷記錄取得此資訊。

如果持續尖峰的100% 正規化 RU/秒耗用量或接近100%，建議您增加輸送量。 您可以利用 Azure 監視器計量和 Azure 監視器記錄，找出哪些作業是繁重的，以及其尖峰使用量。

**正規化的 RU 耗用量**計量也會用來查看哪些資料分割索引鍵範圍在使用方面更暖;因此可讓您將輸送量扭曲到資料分割索引鍵範圍內。 您稍後可以繼續查看 Azure 監視器記錄中的**PartitionKeyRUConsumption**記錄，以取得哪些邏輯分割區索引鍵在使用方面的熱門資訊。

## <a name="view-the-normalized-request-unit-consumption-metric"></a>查看正規化要求單位耗用量度量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 從左側導覽列選取 [監視器]，然後選取 [計量]。

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure 監視器中的 [計量] 窗格":::

3. 從 [計量] 窗格 > **選取資源** > 選擇必要的**訂用帳戶**和**資源群組**。 在 [資源類型] 中，選取 [Azure Cosmos DB 帳戶]，然後選擇其中一個現有的 Azure Cosmos 帳戶並選取 [套用]。

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="選擇 Azure Cosmos 帳戶以查看計量":::

4. 接下來，您可以從可用的計量清單中選取計量。 您可以選取 [要求單位]、[儲存體]、[延遲]、[可用性]、[Cassandra] 等等的專屬計量。 若要深入了解此清單中所有可用的計量，請參閱[依類別區分的計量](monitor-cosmos-db-reference.md)一文。 在此範例中，讓我們選取 [**正規化 RU 耗用量**計量] 和 [**最大**值] 作為匯總值。

   除了這些詳細資料之外，您也可以選取計量的 [時間範圍] 和 [時間細微性]。 在 [最大值] 中，您可以檢視過去 30 天的計量。  套用篩選之後，圖表就會根據您的篩選條件來顯示。

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="從 Azure 入口網站選擇計量":::

### <a name="filters-for-normalized-request-unit-consumption"></a>正規化要求單位耗用量的篩選

您也可以篩選特定**CollectionName**、 **DatabaseName**、 **PartitionKeyRangeID**和**Region**所顯示的計量和圖表。 若要篩選計量，請選取 [**新增篩選**]，然後選擇所需的屬性，例如**CollectionName**和您感興趣的對應值。 圖形接著會顯示所選期間內，針對容器取用的正規化 RU 耗用量單位。  

您可以使用 [套用分割] 選項來將計量分組。  

每個容器的正規化要求單位耗用量計量會顯示如下圖所示：

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="將篩選套用至正規化的要求單位耗用量度量":::

## <a name="next-steps"></a>後續步驟

* 使用 Azure 中的[診斷設定](cosmosdb-monitor-resource-logs.md)來監視 Azure Cosmos DB 資料。
* [Audit Azure Cosmos DB 控制平面作業](audit-control-plane-logs.md)
