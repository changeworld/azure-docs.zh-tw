---
title: 如何在 Azure Cosmos DB 中選擇正確的輸送量供應項目
description: 了解如何針對工作負載，在標準 (手動) 佈建輸送量和自動調整佈建輸送量之間進行選擇。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: fbe17d75ad809c54939624b1409e281b2f62a037
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605200"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>如何在標準 (手動) 和自動調整佈建輸送量之間進行選擇 

Azure Cosmos DB 支援兩種佈建輸送量類型或供應項目：標準 (手動) 和自動調整。 這兩種輸送量類型適用於需要高效能和高度調整的任務關鍵性工作負載，並受到相同 Azure Cosmos DB SLA 的輸送量、可用性、延遲和一致性支援。

本文描述如何針對工作負載，在標準 (手動) 和自動調整佈建輸送量之間進行選擇。 

## <a name="overview-of-provisioned-throughput-types"></a>佈建輸送量類型概觀
請務必先了解佈建輸送量在 Azure Cosmos DB 中的運作方式，再深入探索標準 (手動) 和自動調整之間的差異。 

當使用佈建輸送量時，您可設定工作負載所需的輸送量，並以每秒的要求單位 (RU/秒) 進行測量。 服務會佈建支援輸送量需求所需的容量。 針對服務的資料庫作業 (例如讀取、寫入和查詢) 會使用一些要求單位 (RU) 數量。 深入了解[要求單位](request-units.md)。

下表顯示標準 (手動) 和自動調整之間的高階比較。

|描述|標準 (手動)|Autoscale|
|-------------|------|-------|
|最適用於|流量穩定或可預測的工作負載|流量變動或無法預測的工作負載。 請參閱[自動調整的使用案例](provision-throughput-autoscale.md#use-cases-of-autoscale)。|
|運作方式|您會佈建一段時間內靜態 (除非手動予以變更) 的固定 RU/秒數量 `T`。 每秒最多可使用 `T` RU/秒的輸送量。 <br/><br/>例如，如果設定標準 (手動) 400 RU/秒，則輸送量會維持在 400 RU/秒。|您會設定不想讓系統超過的最高或最大 RU/秒 `Tmax`。 系統會自動調整輸送量 `T`，使 `0.1* Tmax <= T <= Tmax`。 <br/><br/>例如，如果設定 4000 RU/秒的自動調整最大 RU/秒，則系統會在 400 - 4000 RU/秒之間進行調整。|
|使用時機|您想要手動管理輸送量容量 (RU/秒) 並自行調整。<br/><br/>您有很高且一致的佈建 RU/秒使用率。 在一個月的所有時數中，如果設定佈建的 RU/秒 `T`，並在 66% 或更多的時數內使用全額，則標準 (手動) 佈建的 RU/秒預計可省下費用<br/><br/>這是根據設定標準 (手動) 中 `T` 和自動調整中相同數量 `Tmax` 之間的比較而來。 |您想讓 Azure Cosmos DB 根據使用量來管理輸送量容量 (RU/秒) 和規模。<br/><br/>您有變動或難以預測的 RU/秒使用量。 在一個月的所有時數中，如果設定自動調整最大 RU/秒 `Tmax`，並在 66% 或更少的時數內使用全額 `Tmax`，則自動調整預計可為您省下費用。<br/><br/>這是根據設定自動調整 `Tmax` 和標準 (手動) 輸送量中相同數量 `T` 之間的比較而來。|
|計費模式|我們會每小時根據佈建的 RU/秒來收取費用，而不論使用的 RU 數目為何。<br/><br/>範例： <li>佈建 400 RU/秒</li><li>小時 1：沒有要求</li><li>小時 2：400 RU/秒的要求</li><br/><br/>在小時 1 和 2 中，我們會依[標準 (手動) 費率](https://azure.microsoft.com/pricing/details/cosmos-db/)來收取這兩個小時的 400 RU/秒費用。|我們會每小時根據系統在該小時內調整到的最高 RU/秒來收取費用。 <br/><br/>範例： <li>佈建 4000 RU/秒的自動調整最大 RU/秒 (在 400 - 4000 RU/秒之間進行調整)</li><li>小時 1：系統已擴大到 3500 RU/秒的最高值</li><li>小時 2：系統因為沒有使用量而縮小到 400 RU/秒的最小值 (一律為 `Tmax` 的 10%)</li><br/><br/>我們會依[自動調整佈建輸送量費率](https://azure.microsoft.com/pricing/details/cosmos-db/)來收取小時 1 的 3500 RU/秒和小時 2 的 400 RU/秒費用。 每 RU/秒的自動調整費率為 1.5 * 標準 (手動) 費率。
|如果超過佈建的 RU/秒會發生什麼狀況|RU/秒會在佈建的內容保持靜態。 任何在一秒內使用超過佈建 RU 的要求都會有速率限制，並在回應時建議重試前要等候的時間。 您可視需要手動增加或減少 RU/秒。| 系統可將 RU/秒擴大為自動調整最大 RU/秒。 任何在一秒內使用超過自動調整最大 RU/秒的要求都會有速率限制，並在回應時建議重試前要等候的時間。|

## <a name="understand-your-traffic-patterns"></a>了解流量模式

### <a name="new-applications"></a>新的應用程式 ###

如果要建置新的應用程式，但還不知道流量模式，則可能會想要從進入點 RU/秒 (或最小 RU/秒) 開始，以避免一開始就過度佈建。 或者，如果有不需要高度調整的小型應用程式，則可能只想要佈建最小進入點 RU/秒，以將成本最佳化。 針對具有低預期流量的小型應用程式，您也可以考慮 [無伺服器](throughput-serverless.md) 容量模式。

無論您是否打算使用標準 (手動) 或自動調整，以下是您應該考慮的事項：

如果在 400 RU/秒的進入點佈建標準 (手動) RU/秒，除非手動變更輸送量，否則將無法使用超過 400 RU/秒。 我們會每小時依標準 (手動) 佈建輸送量費率來收取 400 RU/秒的費用。

如果在 4000 RU/秒的最大 RU/秒進入點佈建自動調整輸送量，則資源會在 400 到 4000 RU/秒之間進行調整。 由於每 RU/秒的自動調整輸送量計費是標準 (手動) 費率的 1.5 倍，因此在系統已縮小為 400 RU/秒最小值的時數內，您會支付比手動佈建 400 RU/秒時更高的費用。 不過，使用自動調整，如果在任何時候應用程式流量暴增，則最多可使用 4000 RU/秒，而不需要使用者採取動作。 一般來說，您應該將能夠使用最多 RU/秒的優點，與 1.5 倍的自動調整費率進行衡量。

使用 Azure Cosmos DB [容量計算機](estimate-ru-with-capacity-planner.md)來估計輸送量需求。 

### <a name="existing-applications"></a>現有的應用程式 ###

如果有使用標準 (手動) 佈建輸送量的現有應用程式，則可使用 [Azure 監視器計量](../azure-monitor/insights/cosmosdb-insights-overview.md)來判斷流量模式是否適用於自動調整。 

首先，尋找資料庫或容器的[標準化要求單位使用量計量](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric)。 標準化使用率是標準 (手動) 佈建輸送量目前使用量的一個量值。 此數字越接近 100%，就越充分使用所佈建的 RU/秒。 [深入了解](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric)計量。

接下來，判斷標準化使用率在一段時間內的變化。 如果看到標準化使用率是變動或無法預測的，請考慮在資料庫或容器上啟用自動調整。 相反地，如果它是穩定且可預測的，請考慮保持在標準 (手動) 佈建輸送量。 

> [!TIP]
> 透過標準 (手動) 輸送量，則可使用標準化使用率計量來估計在切換至自動調整時可使用的實際 RU/秒。 將某個時間點的標準化使用率乘以目前所佈建標準 (手動) RU/秒。 例如，如果佈建了 5000 RU/秒，且標準化使用率為 90%，則 RU/秒使用量為 0.9 * 5000 = 4500 RU/秒。 如果看到流量模式為變動，但您過度佈建或佈建不足，則可能會想要啟用自動調整，然後據以變更自動調整最大 RU/秒設定。

## <a name="measure-and-monitor-your-usage"></a>測量和監視使用量
經過一段時間，在選擇輸送量類型之後，您應該監視應用程式並視需要進行調整。 

使用自動調整時，請使用 Azure 監視器來查看佈建的自動調整最大 RU/秒 (**自動調整最大輸送量**)，以及系統目前調整到的 RU/秒 (**佈建輸送量**)。 以下是使用自動調整的變動或無法預測工作負載範例。 請注意，當沒有任何流量時，系統會將 RU/秒調整為最小值 (在本案例中為 5000 RU/秒)，也就是最大 RU/秒 (在本案例中為 50,000 RU/秒) 的 10%。 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="使用自動調整的工作負載範例":::

> [!NOTE]
> 當使用標準 (手動) 佈建輸送量時，**佈建輸送量**計量會參考身為使用者所設定的內容。 當使用自動調整輸送量時，此計量會參考系統目前調整到的 RU/秒。

## <a name="next-steps"></a>後續步驟
* 使用 [RU 計算機](https://cosmos.azure.com/capacitycalculator/)來估計新工作負載的輸送量。
* 使用 [Azure 監視器](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db)來監視現有的工作負載。
* 了解如何[在 Azure Cosmos 資料庫或容器上佈建自動調整輸送量](how-to-provision-autoscale-throughput.md)。
* 檢閱[自動調整常見問題集](autoscale-faq.md)。