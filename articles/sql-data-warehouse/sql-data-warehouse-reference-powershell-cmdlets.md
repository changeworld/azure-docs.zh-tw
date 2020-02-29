---
title: PowerShell & REST Api
description: 尋找 Azure Synapse Analytics SQL 集區的熱門 PowerShell Cmdlet，包括如何暫停和繼續資料庫。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0c8b1e9b7526bd45d037f053715613b53ec163f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198451"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>適用于 Azure Synapse Analytics SQL 集區的 PowerShell & REST Api

您可以使用 Azure PowerShell Cmdlet 或 REST Api 來管理許多 Azure Synapse Analytics SQL 集區管理工作。  以下是一些範例，說明如何使用 PowerShell 命令來自動化 SQL 集區中的一般工作。  如需一些良好的 REST 範例，請參閱 [使用 REST 管理延展性](sql-data-warehouse-manage-compute-rest-api.md)一文。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>開始使用 Azure PowerShell Cmdlet

1. 開啟 Windows PowerShell。
2. 在 PowerShell 提示中，執行下列命令來登入 Azure Resource Manager，並選取您的訂用帳戶。
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>暫停資料倉儲範例
暫停 "Server01" 伺服器上託管的 "Database02" 資料庫。  此伺服器位於「ResourceGroup1」這個 Azure 資源群組。

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

一種變化，此範例會使用管線將抓取的物件傳送至[set-azsqldatabase 搭配](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)。  結果就是暫停資料庫。 最終的命令會顯示結果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>啟動資料倉儲範例

繼續 "Server01" 伺服器上託管之 "Database02" 資料庫的作業。 此伺服器包含在「ResourceGroup1」這個資源群組中。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

一種變化，此範例會從 "ResourceGroup1" 資源群組包含的 "Server01" 伺服器中，擷取 "Database02" 資料庫。 它會以管線將抓取的物件傳送至[set-azsqldatabase 搭配](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> 請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為 PowerShell Cmdlet 中的 -ServerName。
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>其他支援的 PowerShell Cmdlet
Azure Synapse Analytics 資料倉儲支援這些 PowerShell Cmdlet。

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [繼續-Set-azsqldatabase 搭配](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [選取-Get-azsubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [暫止-Set-azsqldatabase 搭配](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>後續步驟
如需更多 PowerShell 範例，請參閱：

* [使用 PowerShell 建立資料倉儲](create-data-warehouse-powershell.md)
* [資料庫還原](sql-data-warehouse-restore-database-powershell.md)

如需可使用 PowerShell 自動化的其他工作，請參閱[Azure SQL Database Cmdlet](https://docs.microsoft.com/powershell/module/az.sql)。 並非所有的 Azure SQL Database Cmdlet 都支援 Azure Synapse 分析資料倉儲。  如需可以使用 REST 來自動化的工作清單，請參閱[Azure SQL Database 的作業](/rest/api/sql/)。
