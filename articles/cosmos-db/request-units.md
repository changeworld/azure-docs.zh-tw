---
title: Azure Cosmos DB 中的要求單位和輸送量
description: 了解如何在 Azure Cosmos DB 中指定和預估要求單位需求
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246587"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的要求單位

使用 Azure Cosmos DB，您需要按小時支付所佈建的輸送量及所取用的儲存體。 您必須佈建輸送量，確保有足夠的系統資源隨時都可供您的 Azure Cosmos 資料庫使用。 您需要足夠的資源來滿足或超過 Azure[宇宙 DB SL。](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

Azure Cosmos DB 支援許多 API，例如 SQL、MongoDB、Cassandra、Gremlin 和資料表。 每個 API 都有它自己的一組資料庫作業。 這些作業的範圍可從簡單的點讀取和寫入到複雜查詢。 每個資料庫作業都會根據作業的複雜度來取用系統資源。 

所有資料庫操作的成本都由 Azure Cosmos DB 正常化，並由*請求單位*（簡稱 RUs）表示。 您可以將每秒 RU 想像為輸送量的貨幣。 每秒 RU 是以速率為基礎的貨幣。 它會擷取執行 Azure Cosmos DB 所支援之資料庫作業所需的系統資源 (例如 CPU、IOPS 和記憶體)。 

讀取 1 KB 項目的成本是 1 個要求單位 (即 1 RU)。 存儲每 1 GB 的資料至少需要 10 RU/s。 所有其他資料庫作業同樣會使用 RU 來指派成本。 無論您使用哪種 API 與 Azure Cosmos 容器互動，成本始終由 RU 測量。 無論資料庫作業是寫入、讀取或查詢，成本一律以 RU 為單位進行測量。

下圖顯示 RU 的高階概念：

![資料庫作業會取用要求單位](./media/request-units/request-units.png)

為了管理和規劃容量，Azure Cosmos DB 會確保指定資料集上指定資料庫作業的 RU 數目具有決定性。 您可以檢查回應標頭來追蹤任何資料庫作業所取用的 RU 數目。 當您瞭解影響[RU 費用和](request-units.md#request-unit-considerations)應用程式輸送量要求的因素時，可以經濟高效地運行應用程式。

您還是會以每秒為單位，以遞增 100 個 RU/秒的方式來佈建應用程式的 RU 數目。 若要調整應用程式的佈建輸送量，您可以隨時增加或減少 RU 數目。 您可以用遞增或遞減 100 RU 的方式來調整數目。 您可以程式設計方式或使用 Azure 入口網站進行變更。 您要按小時付費。

您可以在兩個不同的資料粒度上佈建輸送量： 

* **容器**：有關詳細資訊，請參閱[Azure Cosmos 容器上的預配輸送量](how-to-provision-container-throughput.md)。
* **資料庫**：有關詳細資訊，請參閱[在 Azure Cosmos 資料庫上預配輸送量](how-to-provision-database-throughput.md)。

## <a name="request-unit-considerations"></a>要求單位的考量

雖然您預估每秒佈建的的 RU 數目，但請考慮下列因素：

* **項大小**：隨著項大小的增加，用於讀取或寫入物料的 R 並會增加。

* **專案索引**：預設情況下，每個專案都會自動編制索引。 如果您選擇不要在容器中編製某些項目的索引，即會取用較少的 RU。

* **項屬性計數**：假設預設索引在所有屬性上，則寫入項的 R 並會增加。"

* **索引屬性**：每個容器上的索引策略確定預設情況下索引哪些屬性。 若要減少寫入作業的要求單位取用量，請限制已編製索引的屬性數目。

* **資料一致性**：與其他寬鬆的一致性級別相比，強和邊界的陳舊一致性級別在執行讀取操作時消耗的 R 結合大約多兩倍。

* **查詢模式**：查詢的複雜性會影響操作使用多少個 R。 影響查詢作業成本的因素包括： 
    
    - 查詢結果數目
    - 述詞數目
    - 述詞性質
    - 使用者定義函式數目
    - 來源資料的大小
    - 結果集的大小
    - 投影

  Azure Cosmos DB 保證相同資料上相同查詢的成本，一律會與重複執行上的 RU 數目相同。

* **腳本使用**：與查詢一樣，預存程序和觸發器會根據所執行的操作的複雜性消耗 R。 開發應用程式時，請檢查[請求費用標頭](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query)，以更好地瞭解每個操作消耗的 RU 容量。

## <a name="next-steps"></a>後續步驟

* 深入了解如何[在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)。
* 瞭解有關[邏輯分區](partition-data.md)的更多詳細資訊。
* 深入了解如何[全域調整佈建的輸送量](scaling-throughput.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
* 瞭解如何[查找操作的請求單位費用](find-request-unit-charge.md)。
* 瞭解如何在[Azure Cosmos DB 中優化預配輸送量成本](optimize-cost-throughput.md)。
* 瞭解如何在[Azure Cosmos DB 中優化讀取和寫入成本](optimize-cost-reads-writes.md)。
* 瞭解如何在[Azure Cosmos DB 中優化查詢成本](optimize-cost-queries.md)。
* 瞭解如何[使用指標來監視輸送量](use-metrics.md)。
