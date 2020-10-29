---
title: 此 PowerShell 指令碼用以建立 Azure Cosmos DB SQL API 資料庫和容器
description: Azure PowerShell 指令碼 - Azure Cosmos DB 建立 SQL API 資料庫和容器
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 3ee4036a605e74ff43b3951b41ee3b4d1325004d
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482038"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>建立 Azure Cosmos DB 資料庫和容器 - SQL API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

此指令碼會在工作階段層級一致的兩個區域中建立適用於 SQL (核心) API 的 Cosmos 帳戶、建立資料庫，以及建立容器，該容器具有分割區索引鍵、自訂索引編製原則、唯一金鑰原則、TTL、專用輸送量，以及具有自訂衝突解決路徑的「最後寫入者為準」衝突解決原則，以便在 `multipleWriteLocations=true` 時使用。

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

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
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | 建立 Cosmos DB SQL Database。 |
| [New-AzCosmosDBSqlUniqueKey](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | 建立 PSSqlUniqueKey 物件，以作為 New-AzCosmosDBSqlUniqueKeyPolicy 的參數使用。 |
| [New-AzCosmosDBSqlUniqueKeyPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | 建立 PSSqlUniqueKeyPolicy 物件，以作為 New-AzCosmosDBSqlContainer 的參數使用。 |
| [New-AzCosmosDBSqlCompositePath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | 建立 PSCompositePath 物件，以作為 New-AzCosmosDBSqlIndexingPolicy 的參數使用。 |
| [New-AzCosmosDBSqlIncludedPathIndex](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | 建立 PSIndexes 物件，以作為 New-AzCosmosDBSqlIncludedPath 的參數使用。 |
| [New-AzCosmosDBSqlIncludedPath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | 建立 PSIncludedPath 物件，以作為 New-AzCosmosDBSqlIndexingPolicy 的參數使用。 |
| [New-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | 建立 PSSqlIndexingPolicy 物件，以作為 SNew-AzCosmosDBSqlContainer 的參數使用。 |
| [New-AzCosmosDBSqlConflictResolutionPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | 建立 PSSqlConflictResolutionPolicy 物件，以作為 New-AzCosmosDBSqlContainer 的參數使用。 |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | 建立新的 Cosmos DB SQL 容器。 |
|**Azure 資源群組**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。