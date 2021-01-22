---
title: Azure Cosmos DB 資料表 API 輸送量 (RU/秒) 作業的 PowerShell 指令碼
description: Azure Cosmos DB 資料表 API 輸送量 (RU/秒) 作業的 PowerShell 指令碼
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 061bd06c0fa54497d8f00db0e5d4a808e949c2d3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679137"
---
# <a name="throughput-rus-operations-with-powershell-for-a-table-for-azure-cosmos-db---table-api"></a>使用 PowerShell 對 Azure Cosmos DB - 資料表 API 的資料表執行輸送量 (RU/秒) 作業
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

此範例需要 Azure PowerShell Az 5.4.0 或更新版本。 執行 `Get-Module -ListAvailable Az` 可查看已安裝的版本。
如果您需要安裝，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

執行 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 來登入 Azure。

## <a name="get-throughput"></a>取得輸送量

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-get.ps1 "Get throughput on a table for Table API")]

## <a name="update-throughput"></a>更新輸送量

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-update.ps1 "Update throughput on a table for Table API")]

## <a name="migrate-throughput"></a>遷移輸送量

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a table for Table API")]

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
| [Get-AzCosmosDBTableThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbtablethroughput) | 取得指定資料表 API 資料表的輸送量值。 |
| [Update-AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbsqldatabasethroughput) | 更新資料表 API 資料表的輸送量值。 |
| [Invoke-AzCosmosDBTableThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbtablethroughputmigration) | 遷移資料表 API 資料表的輸送量。 |
|**Azure 資源群組**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。