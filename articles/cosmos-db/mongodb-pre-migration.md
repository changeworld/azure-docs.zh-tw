---
title: 用於遷移資料到 Azure Cosmos DB MongoDB API 的預遷移步驟
description: 本文檔概述了從 MongoDB 到 Cosmos DB 的資料移轉的先決條件。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942083"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>從蒙戈DB到 Azure Cosmos DB 的 MongoDB 資料移轉前步驟

在將資料從 MongoDB（本地或雲中）遷移到 Azure Cosmos DB 的蒙戈DB API 之前，您應該：

1. [閱讀有關為 MongoDB 使用 Azure 宇宙 DB API 的關鍵注意事項](#considerations)
2. [選擇遷移資料的選項](#options)
3. [估計工作負載所需的輸送量](#estimate-throughput)
4. [為數據選擇最佳分區鍵](#partitioning)
5. [瞭解可以設置資料的索引策略](#indexing)

如果已完成上述遷移先決條件，則可以使用[Azure 資料庫移轉服務將 MongoDB 資料移轉到 Azure Cosmos DB 的蒙戈DB API。](../dms/tutorial-mongodb-cosmos-db.md) 此外，如果您尚未創建帳戶，則可以流覽顯示創建帳戶步驟的任何[快速入門](create-mongodb-dotnet.md)。

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>為 MongoDB 使用 Azure 宇宙 DB 的 API 時的注意事項

以下是有關蒙高DB的 Azure Cosmos DB API 的特定特徵：

- **容量模型**：Azure Cosmos DB 上的資料庫容量基於基於輸送量的模型。 此模型基於[每秒請求單位](request-units.md)，這是表示每秒可針對集合執行的資料庫運算元的單位。 此容量可以在[資料庫或集合級別](set-throughput.md)分配，也可以在分配模型上進行預配，也可以使用[AutoPilot 模型](provision-throughput-autopilot.md)進行預配。

- **請求單位**：每個資料庫操作在 Azure Cosmos DB 中都有關聯的請求單位 （R）成本。 執行時，將從給定秒的可用請求單位級別中減去。 如果請求需要的 RU 大於當前分配的 RU/s，則有兩個選項可以解決問題 - 增加 RU 量，或等到下一秒啟動，然後重試操作。

- **彈性容量**：給定集合或資料庫的容量可以隨時更改。 這允許資料庫彈性地適應工作負載的輸送量要求。

- **自動分片**：Azure Cosmos DB 提供一個自動分區系統，該系統只需要分片（或分區鍵）。 [自動分區機制](partition-data.md)在所有 Azure Cosmos DB API 之間共用，它允許無縫資料以及通過水準分佈進行整個縮放。

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>用於蒙戈DB的 Azure 宇宙 DB API 的遷移選項

[Azure Cosmos DB 的 MongoDB API 的 Azure 資料庫移轉服務](../dms/tutorial-mongodb-cosmos-db.md)提供了一種機制，通過提供完全託管的託管平臺、遷移監視選項和自動限制處理來簡化資料移轉。 選項的完整清單如下：

|**遷移類型**|**解決方法**|**考慮**|
|---------|---------|---------|
|離線|[資料移轉工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;易於設置和支援多個源 <br/>&bull;不適合大型資料集。|
|離線|[Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;易於設置和支援多個源 <br/>&bull;使用 Azure 宇宙資料庫大容量執行器庫 <br/>&bull;適合大型資料集 <br/>&bull;缺乏檢查點意味著遷移過程中的任何問題都需要重新開機整個遷移過程<br/>&bull;缺少無效信件佇列意味著一些錯誤的檔可能會停止整個遷移過程 <br/>&bull;需要自訂代碼來增加某些資料來源的讀取輸送量|
|離線|[現有蒙戈工具（蒙戈轉儲、mongorestore、Studio3T）](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;易於設置和集成 <br/>&bull;需要自訂油門處理|
|線上|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;完全託管的遷移服務。<br/>&bull;為遷移任務提供託管和監視解決方案。 <br/>&bull;適合大型資料集，並負責複製即時更改 <br/>&bull;僅適用于其他蒙高DB源|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>估計工作負載的輸送量需求

在 Azure Cosmos DB 中，輸送量是預先預配的，以每秒請求單位 （RU） 為單位進行測量。 與 VM 或本機伺服器不同，RAnd 隨時都可以輕鬆向上和向下擴展。 您可以立即更改預配的 R， 如需詳細資訊，請參閱 [Azure Cosmos DB 中的要求單位](request-units.md)。

您可以使用 Azure [Cosmos DB 容量計算機](https://cosmos.azure.com/capacitycalculator/)根據資料庫帳戶配置、資料量、文檔大小以及每秒所需的讀取和寫入來確定請求單位的數量。

以下是影響所需 RA 數量的關鍵因素：
- **文檔大小**：隨著物料/文檔大小的增加，用於讀取或寫入物料/文檔的 R 並會增加。

- **文件屬性計數**：用於創建或更新文檔的 R 並數與其屬性的數量、複雜性和長度有關。 您可以通過[限制索引屬性的數量](mongodb-indexing.md)來減少寫入操作的請求單位消耗。

- **查詢模式**：查詢的複雜性會影響查詢使用的請求單位數。 

瞭解查詢成本的最佳方法是在 Azure Cosmos DB 中使用示例資料，`getLastRequestStastistics`[並使用命令運行 MongoDB Shell 中的依例查詢](connect-mongodb-account.md)，以獲取請求費用，這將輸出消耗的 RU 數：

`db.runCommand({getLastRequestStatistics: 1})`

此命令將輸出類似于以下內容的 JSON 文檔：

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

您還可以使用[診斷設置](cosmosdb-monitor-resource-logs.md)來瞭解針對 Azure Cosmos DB 執行的查詢的頻率和模式。 診斷日誌的結果可以發送到存儲帳戶、事件Hub實例或[Azure 日誌分析](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>選擇分區鍵
分區（也稱為分片）是遷移資料之前需要考慮的關鍵點。 Azure Cosmos DB 使用完全託管分區來增加資料庫中的容量以滿足存儲和輸送量要求。 此功能不需要路由伺服器的託管或配置。   

同樣，分區功能會自動增加容量並相應地重新平衡資料。 有關為數據選擇正確的分區鍵的詳細資訊和建議，請參閱[選擇分區金鑰一文](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。 

## <a name="index-your-data"></a><a id="indexing"></a>為資料編製索引
預設情況下，Azure Cosmos DB 提供對插入的所有資料的自動索引。 Azure Cosmos DB 提供的索引功能包括添加複合索引、唯一索引和存留時間 （TTL） 索引。 索引管理介面映射到命令`createIndex()`。 在[Azure Cosmos DB 的 MongoDB API 中瞭解有關索引](mongodb-indexing.md)的更多資訊。

[Azure 資料庫移轉服務](../dms/tutorial-mongodb-cosmos-db.md)會自動遷移具有唯一索引的 MongoDB 集合。 但是，必須在遷移之前創建唯一索引。 Azure Cosmos DB 不支援創建唯一索引，因為集合中已有資料。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的唯一索引鍵](unique-keys.md)。

## <a name="next-steps"></a>後續步驟
* [使用資料庫移轉服務將 MongoDB 資料移轉到 Cosmos DB。](../dms/tutorial-mongodb-cosmos-db.md) 
* [在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)
* [Azure Cosmos DB 中的資料分割](partition-data.md)
* [Azure 宇宙 DB 中的全域分佈](distribute-data-globally.md)
* [Azure Cosmos DB 中的編製索引](index-overview.md)
* [在 Azure 宇宙資料庫中請求單位](request-units.md)
