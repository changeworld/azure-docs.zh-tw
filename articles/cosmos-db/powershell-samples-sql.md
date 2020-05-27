---
title: 適用於 Azure Cosmos DB 的 Azure PowerShell 範例 - SQL (Core) API
description: 取得 Azure PowerShell 範例，以在 Azure Cosmos DB SQL API 帳戶中執行各種常見工作
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 7cd14151d8cb0fbef7593d62eee050be7987ab18
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713534"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>適用於 Azure Cosmos DB 的 Azure PowerShell 範例 - SQL (Core) API

下表包含適用於 Azure Cosmos DB for SQL (Core) API 的常用 Azure PowerShell 指令碼連結。 如果您想要從 GitHub 存放庫中的 Cosmos DB 派生這些 PowerShell 範例，請造訪 [GitHub 上的 Cosmos DB PowerShell 範例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)。

如需 SQL (核心) API 的其他 Cosmos DB PowerShell 範例及文件，請參閱[使用 PowerShell 管理 Azure Cosmos DB SQL API 資源](manage-with-powershell.md)。 如需其他 API 的 Cosmos DB PowerShell 範例，請參閱 [Cassandra API](powershell-samples-cassandra.md)、[MongoDB API](powershell-samples-mongodb.md)、[Gremlin API](powershell-samples-gremlin.md) 和[資料表 API](powershell-samples-table.md)。

> [!NOTE]
> 這些範例會使用 [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 管理 Cmdlet。 請定期檢查是否有 `Az.CosmosDB` 的更新。

| | |
|---|---|
|[建立帳戶、資料庫和容器](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos DB 帳戶、資料庫和容器。 |
|[使用大型分割區索引鍵建立容器](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用大型分割區索引鍵建立容器。 |
|[列出或取得資料庫或容器](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得資料庫或容器。 |
|[取得 RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得資料庫或容器的 RU/秒。 |
|[更新 RU/秒](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新資料庫或容器的 RU/秒。 |
|[建立沒有索引原則的容器](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立關閉索引原則的 Azure Cosmos 容器。|
|[更新帳戶](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 Cosmos DB 帳戶的預設一致性層級。 |
|[更新帳戶的區域](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 Cosmos DB 帳戶的區域。 |
|[變更容錯移轉優先順序或觸發容錯移轉](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 變更 Azure Cosmos 帳戶的區域容錯移轉優先順序或觸發手動容錯移轉。 |
|[帳戶金鑰或連接字串](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得主要和次要金鑰、連接字串，或重新產生 Azure Cosmos DB 帳戶的帳戶金鑰。 |
|[建立具有 IP 防火牆的 Cosmos 帳戶](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立已啟用 IP 防火牆的 Azure Cosmos DB 帳戶。 |
|||
