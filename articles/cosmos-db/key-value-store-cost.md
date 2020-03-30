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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647513"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure 宇宙 DB 作為索引碼存儲 – 成本概述

Azure Cosmos DB 是全域散發的多模型資料庫服務，可用來輕鬆建置具高可用性的大規模應用程式。 預設情況下，Azure Cosmos DB 自動高效地索引它所引入的所有資料。 這允許對資料進行快速一致的[SQL](how-to-sql-query.md) （和[JavaScript](stored-procedures-triggers-udfs.md)） 查詢。 

本文說明 Azure Cosmos DB 做為金鑰值存放區時，進行簡單寫入與讀取作業的成本。 寫入操作包括插入、替換、刪除和更新資料項目。 除了保證所有多區域帳戶的 99.999% 可用性 SLA 外，Azure Cosmos DB 還提供保證<讀取和（索引）寫入的 10 毫秒延遲（索引），在 99 個百分位數。 

## <a name="why-we-use-request-units-rus"></a>為什麼我們要使用「要求單位」(RU)

Azure Cosmos DB 性能基於[以請求單位](request-units.md)（RU/s） 表示的預配輸送量量。 預配是第二細微性，以 RU/s 購買（[不要與每小時計費混淆](https://azure.microsoft.com/pricing/details/cosmos-db/)）。 應該將統一資源調配視為邏輯抽象（貨幣），它簡化了應用程式所需輸送量的預配。 使用者不必考慮在讀取和寫入輸送量之間進行區分。 RU 的單一貨幣模型可有效率地共用讀取和寫入之間已佈建的容量。 此預配容量模型使服務能夠提供**可預測和一致的輸送量、保證的低延遲和高可用性**。 最後，雖然 RU 模型用於描述輸送量，但每個預配的 RU 也具有定義的資源量（例如記憶體、內核/CPU 和 IOPS）。

作為全球分散式資料庫系統，Cosmos DB 是唯一提供涵蓋延遲、輸送量、一致性和高可用性的全面 SL 的服務。 預配的輸送量將應用於與 Cosmos 帳戶關聯的每個區域。 針對讀取，Cosmos DB 提供多個定義完善的[一致性層級](consistency-levels.md)，以供您選擇。 

下表顯示了基於大小為 1 KB 的資料項目和關閉預設自動索引的 100 KB 的資料項目執行讀取和寫入操作所需的 R 百分比數。 

|項目大小|1 次讀取|1 次寫入|
|-------------|------|-------|
|1 KB|1 RU|5 RU|
|100 KB|10 RU|50 RU|

## <a name="cost-of-reads-and-writes"></a>讀取和寫入的成本

如果您提供 1，000 RU/s，則相當於 360 萬 RU/小時，每小時費用為 0.08 美元（在美國和歐洲）。 對於 1 KB 大小的資料項目，這意味著您可以使用預配輸送量消耗 360 萬次讀取或 0.72 萬次寫入（360 萬 RU / 5）。 標準化為百萬讀取和寫入，成本為 0.022 /000 讀取（0.08 美元 / 3.6 美元）和 0.111 美元/百萬寫入（0.08 美元/ 0.72 美元）。 每百萬成本會變成最小值，如下表所示。

|項目大小|讀取成本為 100 萬次|100 萬寫入成本|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


大部分基本的 Blob 或物件存放區的服務收費，為每百萬次讀取交易 $0.40，以及每百萬次寫入交易 $5。 如果以最佳方式使用，Cosmos DB 可以比這些其他解決方案（對於 1 KB 事務）便宜 98%。

## <a name="next-steps"></a>後續步驟

* 使用[RU 計算機](https://cosmos.azure.com/capacitycalculator/)估計工作負載的輸送量。

