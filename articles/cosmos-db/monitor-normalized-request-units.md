---
title: 監視 Azure Cosmos 容器或帳戶的正規化 RU/秒
description: 瞭解如何在 Azure Cosmos DB 中監視作業的正規化要求單位使用狀況。 Azure Cosmos DB 帳戶的擁有者可以瞭解哪些作業耗用較多的要求單位。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 05/10/2020
ms.openlocfilehash: 23001bdaab0732dbeb088ebadefa90a27e622b19
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118853"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>如何監視 Azure Cosmos 容器或帳戶的正規化 RU/秒

Azure Cosmos DB 的 Azure 監視器會提供計量視圖來監視您的帳戶並建立儀表板。 預設會收集 Azure Cosmos DB 計量，這項功能不會要求您明確啟用或設定任何專案。

**正規化的 RU 耗用量**計量可用來查看複本 wrt 到資料分割索引鍵範圍的要求單位耗用量有多好。 Azure Cosmos DB 會將輸送量平均分散到所有實體分割區。 此計量提供複本集內最大輸送量使用率的每秒查看量。 藉由使用此計量，如果您看到要求單位使用率過高的百分比，您應該增加輸送量以符合工作負載的需求。

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>當正規化 RU/秒較高時所預期的情況

當正規化的 RU/秒耗用量達到100% 時，用戶端會收到速率限制錯誤。 用戶端應該遵守等待時間並重試。 如果達到100% 使用率的短暫尖峰，表示複本上的輸送量已達到其最大效能限制。 例如，在複本上取用所有 RU/秒的預存程式之類的單一作業，將會導致正規化 RU/秒耗用量的短暫尖峰。 在這種情況下，如果要求速率很低，就不會有任何立即的速率限制錯誤。 這是因為，Azure Cosmos DB 可讓要求針對特定要求收取超過布建的 RU/秒，而且該時段內的其他要求會受到速率限制。

Azure 監視器計量可協助您使用 [**要求總數**] 度量來尋找每個狀態碼的作業。 稍後您可以根據429狀態碼來篩選這些要求，並依作業**類型**進行分割。

若要尋找速率受限的要求，建議的方法是透過診斷記錄取得此資訊。

如果持續尖峰的100% 正規化 RU/秒耗用量或接近100%，建議您增加輸送量。 您可以利用 Azure 監視器計量和 Azure 監視器記錄，找出哪些作業是繁重的，以及其尖峰使用量。

**正規化的 RU 耗用量**計量也會用來查看哪些資料分割索引鍵範圍在使用方面更暖;因此可讓您將輸送量扭曲到資料分割索引鍵範圍內。 您稍後可以繼續查看 Azure 監視器記錄中的**PartitionKeyRUConsumption**記錄，以取得哪些邏輯分割區索引鍵在使用方面的熱門資訊。

## <a name="view-the-normalized-request-unit-consumption-metric"></a>查看正規化要求單位耗用量度量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 從左側導覽列選取 [**監視**]，然後選取 [**計量**]。

   ![Azure 監視器中的 [計量] 窗格](./media/monitor-normalized-request-units/monitor-metrics-blade.png)

3. 從 [**計量**] 窗格 >**選取資源**> 選擇所需的**訂**用帳戶和**資源群組**。 針對 [**資源類型**]，選取 [ **Azure Cosmos DB 帳戶**] **，選擇其中**一個現有的 Azure Cosmos 帳戶，然後選取 [套用]。

   ![選擇 Azure Cosmos 帳戶以查看計量](./media/monitor-normalized-request-units/select-cosmos-db-account.png)

4. 接下來，您可以從可用的計量清單中選取度量。 您可以選取 [要求單位]、[儲存體]、[延遲]、[可用性]、[Cassandra] 和其他的特定計量。 若要深入瞭解此清單中所有可用的計量，請參閱[依類別](monitor-cosmos-db-reference.md)區分的計量一文。 在此範例中，讓我們選取 [**正規化 RU 耗用量**計量] 和 [**最大**值] 作為匯總值。

   除了這些詳細資料之外，您也可以選取度量的**時間範圍**和**時間細微性**。 在 [最大值] 中，您可以查看過去30天的計量。  套用篩選之後，就會根據您的篩選器來顯示圖表。

   ![從 Azure 入口網站選擇度量](./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png)

### <a name="filters-for-normalized-request-unit-consumption"></a>正規化要求單位耗用量的篩選

您也可以篩選特定**CollectionName**、 **DatabaseName**、 **PartitionKeyRangeID**和**Region**所顯示的計量和圖表。 若要篩選計量，請選取 [**新增篩選**]，然後選擇所需的屬性，例如**CollectionName**和您感興趣的對應值。 圖形接著會顯示所選期間內，針對容器取用的正規化 RU 耗用量單位。  

您可以使用 [套用**分割**] 選項將計量分組。  

每個容器的正規化要求單位耗用量計量會顯示如下圖所示：

![將篩選套用至正規化的要求單位耗用量度量](./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png)

## <a name="next-steps"></a>後續步驟

* 使用 Azure 中的[診斷設定](cosmosdb-monitor-resource-logs.md)來監視 Azure Cosmos DB 資料。
* [Audit Azure Cosmos DB 控制平面作業](audit-control-plane-logs.md)