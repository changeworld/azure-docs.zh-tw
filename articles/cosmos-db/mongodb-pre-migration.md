---
title: 將資料移轉至「適用於 MongoDB 的 Azure Cosmos DB API」的預先移轉步驟
description: 此文件概述從 MongoDB 到 Cosmos DB 的資料移轉必要條件。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 8156c1c3601b0cd6f518f6a70bc4e0769c570e7f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647286"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>將資料從 MongoDB 移轉到「適用於 MongoDB 的 Azure Cosmos DB API」的預先移轉步驟

將資料從 MongoDB (內部部署或在雲端中) 移轉到「適用於 MongoDB 的 Azure Cosmos DB API」之前，您應該：

1. [閱讀使用適用於 MongoDB 的 Azure Cosmos DB API 的重要考量事項](#considerations)
2. [選擇選項以移轉您的資料](#options)
3. [估計您的工作負載所需的輸送量](#estimate-throughput)
4. [為您的資料選擇最佳的分割區索引鍵](#partitioning)
5. [了解您可以在資料上設定的編製索引原則](#indexing)

如果您已完成上述的移轉必要條件，可以[使用 Azure Database Migration Service 將 MongoDB 資料移轉至適用於 MongoDB 的 Azure Cosmos DB API](../dms/tutorial-mongodb-cosmos-db.md)。 此外，如果您尚未建立帳戶，您可以瀏覽任何一個顯示建立帳戶步驟的[快速入門](create-mongodb-dotnet.md)。

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>使用適用於 MongoDB 的 Azure Cosmos DB API 的考量事項

以下是關於「適用於 MongoDB 的 Azure Cosmos DB API」的特色：

- **容量模型**：Azure Cosmos DB 的資料庫容量是以輸送量為基礎的模型做為根據。 此模型是以[每秒要求單位](request-units.md)為基礎，此單位表示每秒鐘可對一個集合所執行的資料庫作業數目。 此容量可以在[資料庫或集合層級](set-throughput.md)進行配置，而且可以在配置模型上佈建容量，或使用[自動調整佈建輸送量](provision-throughput-autoscale.md)。

- **要求單位**：每個資料庫作業在 Azure Cosmos DB 中都有相關聯的要求單位 (RU) 成本。 執行時，會從給定秒數的可用要求單位層級減去此要求單位。 如果要求所需的 RU 超過目前配置的 RU/秒，有兩個選項可解決此問題 - 增加 RU 數量，或等到下一秒開始後再重試作業。

- **彈性容量**：給定集合或資料庫的容量可隨時變更。 這讓資料庫可彈性地適應工作負載的輸送量需求。

- **自動分區化**：Azure Cosmos DB 提供一個只需要分區 (或分割區索引鍵) 的自動資料分割系統。 [自動資料分割機制](partition-data.md)會在所有 Azure Cosmos DB API 之間共用，可透過水平發佈，順暢地進行資料和輸送量調整。

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>「適用於 MongoDB 的 Azure Cosmos DB API」的移轉選項

[「適用於 MongoDB 的 Azure Cosmos DB API」所適用的 Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) 藉由提供完全受控託管平台、移轉監視選項和自動節流處理，提供簡化資料移轉的機制。 完整的選項清單如下所示：

|**移轉類型**|**方案**|**考量**|
|---------|---------|---------|
|離線|[資料移轉工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; 易於設定，並支援多個來源 <br/>&bull; 不適用於大型資料集。|
|離線|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; 易於設定，並支援多個來源 <br/>&bull; 使用 Azure Cosmos DB 大量執行工具程式庫 <br/>&bull; 適合大型資料集 <br/>&bull; 缺少檢查點表示在移轉過程中出現的任何問題，皆需要重新啟動整個移轉流程<br/>&bull; 缺少無效信件佇列表示，只要有幾個錯誤的檔案就可能會停止整個移轉流程 <br/>&bull; 需要自訂程式碼，以增加特定資料來源的讀取輸送量|
|離線|[現有的 Mongo 工具 (mongodump、mongorestore、Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; 易於設定和整合 <br/>&bull; 需要自訂處理節流|
|線上|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; 完全受控的移轉服務。<br/>&bull; 為遷移工作提供託管和監視解決方案。 <br/>&bull; 適用於大型資料集，並負責複寫即時變更 <br/>&bull; 僅適用於其他 MongoDB 來源|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> 估計您的工作負載所需的輸送量

在 Azure Cosmos DB 中，會事先佈建輸送量，並以每秒的要求單位 (RU) 進行測量。 不同於 VM 或內部部署伺服器，RU 可以輕鬆地隨時擴大或縮小。 您可以立即變更佈建的 RU 數目。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的要求單位](request-units.md)。

您可以使用 [Azure Cosmos DB 容量計算機](https://cosmos.azure.com/capacitycalculator/)，根據資料庫帳戶設定、資料量、文件大小和所需的每秒讀寫次數，來確定要求單位的數量。

以下是影響所需 RU 數量的重要因素：
- **文件大小**：當項目/文件大小增加時，取用來讀取或寫入該項目/文件的 RU 數目也會增加。

- **文件屬性計數**：建立或更新文件所耗用的 RU 數目，與數量、複雜性及其屬性長度有關。 您可以藉由[限制已編製索引的屬性數目](mongodb-indexing.md)，來減少寫入作業的要求單位取用量。

- **查詢模式**：查詢的複雜性會影響查詢用的要求單位數目。 

了解查詢成本的最佳方式是，使用 Azure Cosmos DB 中的樣本資料，並使用 `getLastRequestStastistics` 命令，[從 MongoDB Shell 執行樣本查詢](connect-mongodb-account.md)，以取得要求費用，這將會輸出取用的 RU 數量：

`db.runCommand({getLastRequestStatistics: 1})`

此命令將輸出 JSON 文件，如下所示：

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

您也可以使用[診斷設定](cosmosdb-monitor-resource-logs.md)，以了解針對 Azure Cosmos DB 執行查詢的頻率和模式。 診斷記錄的結果可傳送至儲存體帳戶、EventHub 執行個體或 [Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>選擇分割區索引鍵
資料分割 (也稱為「分區化」) 是移轉資料前的考量要點。 Azure Cosmos DB 使用完全受控的資料分割，增加資料庫中的容量，以滿足儲存和輸送量的需求。 此功能不需要裝載或設定路由伺服器。   

同樣地，資料分割容量會自動新增容量，並視情況重新平衡資料。 如需為您的資料選擇正確分割區的詳細資料和建議，請參閱[選擇分割區索引鍵一文](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。 

## <a name="index-your-data"></a><a id="indexing"></a>為資料編製索引
根據預設，Azure Cosmos DB 會針對插入的所有資料提供自動編制索引。 Azure Cosmos DB 提供的編制索引功能包括新增複合式索引、唯一索引和存留時間 (TTL) 索引。 索引管理介面對應至 `createIndex()` 命令。 深入了解[適用於 MongoDB 的 Azure Cosmos DB API 中的索引編製](mongodb-indexing.md)。

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) 會自動移轉具有唯一索引的 MongoDB 集合。 不過，必須先建立唯一索引，然後再進行移轉。 當您的集合中已經有資料時，Azure Cosmos DB 不支援建立唯一索引。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的唯一索引鍵](unique-keys.md)。

## <a name="next-steps"></a>後續步驟
* [使用 Database Migration Service 將您的 MongoDB 資料移轉至 Cosmos DB。](../dms/tutorial-mongodb-cosmos-db.md) 
* [在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)
* [Azure Cosmos DB 中的資料分割](partition-data.md)
* [Azure Cosmos DB 中的全域散發](distribute-data-globally.md)
* [Azure Cosmos DB 中的編製索引](index-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
