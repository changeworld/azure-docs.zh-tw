---
title: 適用於 Azure Cosmos DB 作為索引鍵值存放區的要求單位費用
description: 深入了解 Azure Cosmos DB 做為金鑰值存放區時，進行簡單寫入與讀取作業的要求單位費用。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77647513"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB 做為金鑰值存放區–成本總覽

Azure Cosmos DB 是全域散發的多模型資料庫服務，可用來輕鬆建置具高可用性的大規模應用程式。 根據預設，Azure Cosmos DB 會自動並有效率地為內嵌的所有資料編制索引。 這可讓您對資料進行快速且一致的[SQL](how-to-sql-query.md) （和[JavaScript](stored-procedures-triggers-udfs.md)）查詢。 

本文說明 Azure Cosmos DB 做為金鑰值存放區時，進行簡單寫入與讀取作業的成本。 寫入作業包括資料項目目的插入、取代、刪除和更新插入。 除了保證所有多重區域帳戶99.999% 的可用性 SLA 外，Azure Cosmos DB 提供保證 <10 毫秒的讀取延遲和（索引）寫入（第99個百分位數）。 

## <a name="why-we-use-request-units-rus"></a>為什麼我們要使用「要求單位」(RU)

Azure Cosmos DB 效能是以[要求單位](request-units.md)（RU/秒）表示的布建輸送量為基礎。 布建為第二個數據細微性，且以 RU/秒購買（[不會與每小時計費混淆](https://azure.microsoft.com/pricing/details/cosmos-db/)）。 Ru 應該被視為邏輯抽象（貨幣），可簡化應用程式所需的輸送量布建。 使用者不需要考慮區分讀取和寫入輸送量。 RU 的單一貨幣模型可有效率地共用讀取和寫入之間已佈建的容量。 此布建容量模型可讓服務提供**可預測且一致的輸送量、保證低延遲，以及高可用性**。 最後，使用 RU 模型來描述輸送量時，每個布建的 RU 也會有定義的資源數量（例如，記憶體、核心/CPU 和 IOPS）。

作為全域散發的資料庫系統，Cosmos DB 是唯一的 Azure 服務，可提供涵蓋延遲、輸送量、一致性和高可用性的完整 Sla。 您所布建的輸送量會套用到與您的 Cosmos 帳戶相關聯的每個區域。 針對讀取，Cosmos DB 提供多個定義完善的[一致性層級](consistency-levels.md)，以供您選擇。 

下表顯示執行讀取和寫入作業所需的 ru 數目，其依據的是預設自動編制索引關閉的大小為 1 KB 和 100 Kb 的資料項目。 

|項目大小|1 次讀取|1 次寫入|
|-------------|------|-------|
|1 KB|1 RU|5 RU|
|100 KB|10 RU|50 RU|

## <a name="cost-of-reads-and-writes"></a>讀取和寫入的成本

如果您布建 1000 RU/秒，此金額為 3600000 RU/小時，且將會以小時（美國和歐洲）的成本 $0.08。 針對 1 KB 大小的資料項目，這表示您可以使用布建的輸送量來取用3600000讀取或720000寫入（3600000 RU/5）。 正規化為百萬次讀取和寫入，成本會是 $0.022/百萬的讀取（$0.08/3.6）和 $ 0.111/百萬寫入（$0.08/0.72）。 每百萬成本會變成最小值，如下表所示。

|項目大小|1000000讀取的成本|1000000寫入的成本|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


大部分基本的 Blob 或物件存放區的服務收費，為每百萬次讀取交易 $0.40，以及每百萬次寫入交易 $5。 如果以最佳方式使用，則比起其他解決方案（1 KB 交易），Cosmos DB 的成本可以高達98%。

## <a name="next-steps"></a>後續步驟

* 使用[RU 計算機](https://cosmos.azure.com/capacitycalculator/)來預估工作負載的輸送量。

