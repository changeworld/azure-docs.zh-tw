---
title: 適用於 Azure Cosmos DB 作為索引鍵值存放區的要求單位費用
description: 深入了解 Azure Cosmos DB 做為金鑰值存放區時，進行簡單寫入與讀取作業的要求單位費用。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 9354ae0a22ef2e8ab4ee6a57563d3f3c4c8e4547
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339292"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB 做為金鑰值存放區–成本總覽
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 是全域散發的多模型資料庫服務，可用來輕鬆建置具高可用性的大規模應用程式。 依預設，Azure Cosmos DB 會自動並有效率地編制其內嵌的所有資料索引。 這可讓您快速且一致地對資料進行 [SQL](./sql-query-getting-started.md) (和 [JavaScript](stored-procedures-triggers-udfs.md)) 查詢。 

本文說明 Azure Cosmos DB 做為金鑰值存放區時，進行簡單寫入與讀取作業的成本。 寫入作業包括資料項目目的插入、取代、刪除和 upsert。 除了保證所有多重區域帳戶99.999% 的可用性 SLA 之外，Azure Cosmos DB 可保證在第99個百分位數 <10 毫秒延遲的讀取和 (索引) 寫入。 

## <a name="why-we-use-request-units-rus"></a>為什麼我們要使用「要求單位」(RU)

Azure Cosmos DB 的效能取決於 [要求單位中以要求單位](request-units.md) （ (RU/秒) ）表示的布建輸送量量。 布建為第二個數據細微性，且以 RU/秒購買 ([不會與每小時計費) 混淆](https://azure.microsoft.com/pricing/details/cosmos-db/) 。 Ru 應視為邏輯抽象 (貨幣) ，可簡化應用程式所需的輸送量布建。 使用者不需要考慮區分讀取和寫入輸送量。 RU 的單一貨幣模型可有效率地共用讀取和寫入之間已佈建的容量。 此布建容量模型可讓服務提供 **可預測且一致的輸送量、保證低延遲和高可用性** 。 最後，雖然 RU 模型是用來描述輸送量，但每個布建的 RU 也會有定義的資源數量 (例如，記憶體、核心/CPU 和 IOPS) 。

做為全域散發的資料庫系統，Cosmos DB 是唯一提供涵蓋延遲、輸送量、一致性和高可用性的完整 Sla 的 Azure 服務。 您布建的輸送量會套用到與您的 Cosmos 帳戶相關聯的每個區域。 針對讀取，Cosmos DB 提供多個定義完善的[一致性層級](consistency-levels.md)，以供您選擇。 

下表根據 Kb 預設自動編制索引的大小為 1 KB 和100的資料項目，顯示執行讀取和寫入作業所需的 ru 數目。 

|項目大小|1 次讀取|1 次寫入|
|-------------|------|-------|
|1 KB|1 RU|5 RU|
|100 KB|10 RU|50 RU|

## <a name="cost-of-reads-and-writes"></a>讀取和寫入的成本

如果您布建 1000 RU/秒，此數量會是 3600000 RU/小時，而且將會在美國和歐洲)  (小時的成本 $0.08。 若為 1 KB 大小的資料項目，這表示您可以使用您布建的輸送量 (3600000 RU/5) 來取用3600000讀取或720000寫入。 正規化至一百萬次的讀取和寫入，成本會是 $0.022/百萬的讀取 ($0.08/3.6) 和 $ 0.111/百萬項寫入 ($0.08/0.72) 。 每百萬成本會變成最小值，如下表所示。

|項目大小|1000000讀取的成本|1000000寫入成本|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


大部分基本的 Blob 或物件存放區的服務收費，為每百萬次讀取交易 $0.40，以及每百萬次寫入交易 $5。 如果以最佳方式使用，Cosmos DB 可) 的 1 KB 交易 (，比這些其他解決方案更便宜98%。

## <a name="next-steps"></a>後續步驟

* 使用 [RU 計算機](https://cosmos.azure.com/capacitycalculator/) 來預估工作負載的輸送量。