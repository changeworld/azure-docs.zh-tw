---
title: 用來建立具有自動調整功能之 Azure Cosmos DB Cassandra API keyspace 和資料表的 PowerShell 指令碼
description: Azure PowerShell 指令碼 - Azure Cosmos DB 建立具有自動調整功能的 Cassandra API Kyspace 和資料表
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: a32ab1b411fd84d36ef6efefcb63f80efdaab301
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684447"
---
# <a name="create-a-keyspace-and-table-with-autoscale-for-azure-cosmos-db---cassandra-api"></a>針對 Azure Cosmos DB，建立具有自動調整功能的 Keyspace 和資料表 - Cassandra API
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

此範例需要 Azure PowerShell Az 5.4.0 或更新版本。 執行 `Get-Module -ListAvailable Az` 可查看已安裝的版本。
如果您需要安裝，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

執行 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 來登入 Azure。

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-autoscale.ps1 "Create a keyspace and table with autoscale for Cassandra API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | 建立 Cosmos DB 帳戶。 |
| [New-AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/new-azcosmosdbcassandrakeyspace) | 建立 Cosmos DB Cassandra API Keyspace。 |
| [New-AzCosmosDBCassandraClusterKey](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | 建立 Cosmos DB Cassandra API 叢集金鑰。 |
| [New-AzCosmosDBCassandraColumn](/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | 建立 Cosmos DB Cassandra API 資料行。 |
| [New-AzCosmosDBCassandraSchema](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | 建立 Cosmos DB Cassandra API 結構描述。 |
| [New-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/new-azcosmosdbcassandratable) | 建立 Cosmos DB Cassandra API 資料表。 |
|**Azure 資源群組**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)
