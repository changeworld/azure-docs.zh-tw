---
title: 在 Azure Cosmos DB 中使用全域散發分析的解決方案。
description: 深入瞭解可使用 Azure Cosmos DB 中的全域分散式分析來建立的解決方案。
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73681798"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用全域散發分析的解決方案

本文說明可使用 Azure Cosmos DB 中的全域分散式分析來建立的解決方案。

## <a name="retail-and-consumer-goods"></a>零售與消費性商品

您可以使用 Azure Cosmos DB 中的 Spark 支援來提供即時建議和供應項目。 您可以協助客戶透過即時個人化和產品建議探索他們所需的專案。

* 您可以使用 Apache Spark 執行階段所提供的內建 Machine Learning 支援，產生各個產品目錄的即時建議。

* 您可以採擷點選流資料、購買資料和客戶資料，以提供可推動存留期價值的目標建議。

* 使用 Azure Cosmos DB 的全域散發功能時，分散於不同區域的大量產品資料將可在數毫秒內完成分析。

* 您可以快速深入了解分散各地的使用者和資料。 您可以在適當時機將適當的廣告提供給對的使用者，以提升促銷轉換率。

* 您可以利用內建的 Spark 串流功能，藉由將即時資料與靜態客戶資料結合來擴充資料。 如此，您就可以即時提供更加個人化、目標更明確的廣告，並且與客戶的行為緊密呼應。

下圖顯示如何使用 Azure Cosmos DB Spark 支援來最佳化定價和促銷：

![最佳化定價和促銷的 Azure Cosmos DB Spark 支援](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


下圖顯示如何在即時建議引擎中使用 Azure Cosmos DB Spark 支援：

![即時建議引擎中的 Azure Cosmos DB Spark 支援](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>製造和 IoT

Azure Cosmos DB 的內建分析平台可讓您從全球各地的數百萬個裝置啟用 IoT 資料的即時分析。 您可以進行現代化的創新，例如預測天氣模式、預測性分析和能源最佳化。

* 藉由使用 Azure Cosmos DB，您可以採擷即時資產計量和天氣因素之類的資料，然後套用智慧電網分析，以最佳化現場連線裝置的效能。 智慧電網分析是控制營運成本、提升電網可靠性，以及為消費者提供個人化能源服務的重要關鍵。

下圖顯示如何使用 Azure Cosmos DB 的 Spark 支援讀取來自 IoT 裝置的計量，並套用智慧電網分析：

![讀取 IoT 裝置計量的 Azure Cosmos DB Spark 支援](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>預測性維護

* 維護深水平台的小型鑽探設備所使用的資產 (例如壓縮機)，是一項複雜的工作。 這些資產分佈於全球各地，並產生數以 PB 計的資料。 藉由使用 Azure Cosmos DB，您可以建置端對端預測性資料管線，以使用 Spark 串流來處理大量的感應器遙測資料、儲存資產組件和感應器對應資料。

* 您可以建置並部署機器學習模型，以在資產故障發生之前先行預測，並發出維護工單。

下圖顯示如何使用 Azure Cosmos DB Spark 支援來建置預測性維護系統：

![建置預測性維護系統的 Azure Cosmos DB Spark 支援](./media/analytics-solution-architectures/predictive-maintenance-system.png)

下圖顯示如何使用 Azure Cosmos DB Spark 支援來建置即時車輛診斷系統：

![建置即時車輛診斷系統的 Azure Cosmos DB Spark 支援](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>遊戲

* 透過內建 Spark 支援，Azure Cosmos DB 可讓您在短短幾分鐘內輕鬆建立、調整及部署進階分析和機器學習模型，以產生最理想的遊戲體驗。

* 您可以分析玩家、購買和行為資料以建立相關的個人化供應項目，進而達到高轉換率。

* 使用 Spark 機器學習，可以分析並深入了解遊戲的遙測資料。 您可以診斷及防止載入時間緩慢和遊戲中的問題。

下圖顯示如何在遊戲分析中使用 Azure Cosmos DB Spark 支援：

![遊戲分析中的 Azure Cosmos DB Spark 支援](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Azure Cosmos DB 的優點，請參閱[概觀](introduction.md)一文。
* [開始使用適用於 MongoDB 的 Azure Cosmos DB API](mongodb-introduction.md)
* [開始使用 Azure Cosmos DB Cassandra API](cassandra-introduction.md)
* [開始使用 Azure Cosmos DB Gremlin API](graph-introduction.md)
* [開始使用 Azure Cosmos DB 資料表 API](table-introduction.md)
