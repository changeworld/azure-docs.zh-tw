---
title: 用來建立具有自動調整功能之 Azure Cosmos DB SQL API 資料庫和容器的 PowerShell 指令碼
description: Azure PowerShell 指令碼 - Azure Cosmos DB 建立具有自動調整功能的 SQL API 資料庫和容器
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 32ebbb43fc02a3aa3d5129ea66ebe8640d11b0cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87504791"
---
# <a name="create-a-database-and-container-with-autoscale-for-azure-cosmos-db---sql-api"></a>針對 Azure Cosmos DB，建立具有自動調整功能的資料庫和容器 - SQL API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

本指令碼會在兩個區域中，使用工作階段等級一致性、資料庫和具有自動調整輸送量和預設索引原則的容器建立適用於 Core (SQL) API 的 Cosmos 帳戶。

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-autoscale.ps1 "Create an account, database, and container with autoscale for Core (SQL) API")]

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
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | 建立新的 Cosmos DB SQL 容器。 |
|**Azure 資源群組**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。

您可以在 [Azure Cosmos DB PowerShell 指令碼](../../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 指令碼範例。
