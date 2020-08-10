---
title: 適用於 Azure Cosmos DB 的 Azure PowerShell 範例
description: 取得 Azure PowerShell 範例，以在 Azure Cosmos DB 中執行各種常見工作
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 678e9490fe52a329365174e1a2283a475f3312e5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505011"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure PowerShell 範例

下表包含適用於 Azure Cosmos DB 的常用 Azure PowerShell 指令碼連結。 使用右側的連結瀏覽至 API 特定範例。 所有 API 的通用範例皆相同。 您可以在 [Azure PowerShell 參考](/powershell/module/az.cosmosdb)中取得所有 Azure Cosmos DB PowerShell Cmdlet 的參考頁面。 請定期檢查是否有 `Az.CosmosDB` 的更新。 您也可以從 GitHub 存放庫 ([GitHub 上的 Cosmos DB PowerShell 範例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)) 的 Cosmos DB 派生這些 PowerShell 範例 。

## <a name="common-samples"></a>通用範例

|Task | 描述 |
|---|---|
|[更新帳戶](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 Cosmos DB 帳戶的預設一致性層級。 |
|[更新帳戶的區域](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 Cosmos DB 帳戶的區域。 |
|[變更容錯移轉優先順序或觸發容錯移轉](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 變更 Azure Cosmos 帳戶的區域容錯移轉優先順序或觸發手動容錯移轉。 |
|[帳戶金鑰或連接字串](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得主要和次要金鑰、連接字串，或重新產生 Azure Cosmos DB 帳戶的帳戶金鑰。 |
|[建立具有 IP 防火牆的 Cosmos 帳戶](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立已啟用 IP 防火牆的 Azure Cosmos DB 帳戶。 |
|||

## <a name="core-sql-api-samples"></a>Core (SQL) API 範例

|Task | 描述 |
|---|---|
|[建立帳戶、資料庫和容器](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos DB 帳戶、資料庫和容器。 |
|[建立具有自動調整功能的帳戶、資料庫和容器](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立具有自動調整功能的 Azure Cosmos DB 帳戶、資料庫和容器。 |
|[使用大型分割區索引鍵建立容器](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用大型分割區索引鍵建立容器。 |
|[建立沒有索引原則的容器](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立關閉索引原則的 Azure Cosmos 容器。|
|[列出或取得資料庫或容器](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得資料庫或容器。 |
|[取得輸送量](scripts/powershell/sql/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得資料庫或容器的輸送量。 |
|[更新輸送量](scripts/powershell/sql/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新資料庫或容器的輸送量。 |
|[鎖定資源避免刪除](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用資源鎖定避免刪除資源。 |
|||

## <a name="cassandra-api-samples"></a>Cassandra API 範例

|Task | 描述 |
|---|---|
|[建立帳戶、keyspace 和資料表](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶、keyspace 和資料表。 |
|[建立具有自動調整功能的帳戶、keyspace 和資料表](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立具有自動調整功能的 Azure Cosmos 帳戶、keyspace 和資料表。 |
|[列出或取得 keyspace 或資料表](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得 keyspace 或資料表。 |
|[取得輸送量](scripts/powershell/cassandra/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得 keyspace 或資料表的輸送量。 |
|[更新輸送量](scripts/powershell/cassandra/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 keyspace 或資料表的輸送量。 |
|[鎖定資源避免刪除](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用資源鎖定避免刪除資源。 |
|||

## <a name="mongo-db-api-samples"></a>Mongo DB API 範例

|Task | 描述 |
|---|---|
|[建立帳戶、資料庫和集合](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶、資料庫和集合。 |
|[建立具有自動調整功能的帳戶、資料庫和集合](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立具有自動調整功能的 Azure Cosmos 帳戶、資料庫和集合。 |
|[列出或取得資料庫或集合](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得資料庫或集合。 |
|[取得輸送量](scripts/powershell/mongodb/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得資料庫或集合的輸送量。 |
|[更新輸送量](scripts/powershell/mongodb/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新資料庫或集合的輸送量。 |
|[鎖定資源避免刪除](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用資源鎖定避免刪除資源。 |
|||

## <a name="gremlin-api-samples"></a>Gremlin API 範例

|Task | 描述 |
|---|---|
|[建立帳戶、資料庫和圖表](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶、資料庫和圖表。 |
|[建立具有自動調整功能的帳戶、資料庫和圖表](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立具有自動調整功能的 Azure Cosmos 帳戶、資料庫和圖表。 |
|[列出或取得資料庫或圖表](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得資料庫或圖表。 |
|[取得輸送量](scripts/powershell/gremlin/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得資料庫或圖表的輸送量。 |
|[更新輸送量](scripts/powershell/gremlin/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新資料庫或圖表的輸送量。 |
|[鎖定資源避免刪除](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用資源鎖定避免刪除資源。 |
|||

## <a name="table-api-samples"></a>資料表 API 範例

|Task | 描述 |
|---|---|
|[建立帳戶和資料表](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶和資料表。 |
|[建立具有自動調整功能的帳戶和資料表](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶和資料表自動調整。 |
|[列出或取得資料表](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得資料表 |
|[取得輸送量](scripts/powershell/table/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得資料表的輸送量。 |
|[更新輸送量](scripts/powershell/table/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新資料表的輸送量。 |
|[鎖定資源避免刪除](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用資源鎖定避免刪除資源。 |
|||
