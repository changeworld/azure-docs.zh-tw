---
title: 此 PowerShell 指令碼用以列出並取得 Azure Cosmos DB Cassandra API 資源
description: Azure PowerShell 指令碼 - Azure Cosmos DB 列出及取得 Cassandra API 的作業
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: c413a1dedbdd39ea27ad4dea3559d79b2a57b1f5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684399"
---
# <a name="list-and-get-keyspaces-and-tables-for-azure-cosmos-db---cassandra-api"></a>列出及取得 Azure Cosmos DB Kyspace 和資料表 - Cassandra API
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

此範例需要 Azure PowerShell Az 5.4.0 或更新版本。 執行 `Get-Module -ListAvailable Az` 可查看已安裝的版本。
如果您需要安裝，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

執行 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 來登入 Azure。

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-list-get.ps1 "List or get keyspace or table for Cassandra API")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | 列出 Cosmos DB 帳戶，或取得指定的 Cosmos DB 帳戶。 |
| [Get-AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspace) | 列出帳戶中的 Cosmos DB Cassandra API Keyspace，或取得帳戶中指定的 Cosmos DB Cassandra API Keyspace。 |
| [Get-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/get-azcosmosdbcassandratable) | 列出 Keyspace 中的 Cosmos DB Cassandra API 資料表，或取得 Keyspace 中指定的 Cosmos DB Cassandra API 資料表。 |
|**Azure 資源群組**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。