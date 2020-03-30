---
title: 電源外殼& REST API
description: 查找 Azure Synapse 分析 SQL 池的頂級 PowerShell Cmdlet，包括如何暫停和恢復資料庫。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351394"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>適用于 Azure 突觸分析 SQL 池的 PowerShell & REST API

許多 Azure 突觸分析 SQL 池管理工作可以使用 Azure PowerShell Cmdlet 或 REST API 進行管理。  下面是如何使用 PowerShell 命令自動執行 SQL 池中常見任務的一些示例。  如需一些良好的 REST 範例，請參閱[使用 REST 管理延展性](sql-data-warehouse-manage-compute-rest-api.md)一文。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>開始使用 Azure PowerShell Cmdlet

1. 開啟 Windows PowerShell。
2. 在 PowerShell 提示中，執行下列命令來登入 Azure Resource Manager，並選取您的訂用帳戶。
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>暫停資料倉儲示例
暫停 "Server01" 伺服器上託管的 "Database02" 資料庫。  此伺服器位於「ResourceGroup1」這個 Azure 資源群組。

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

變體，此示例將檢索到的物件管道到[掛起-AzSql 資料庫](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)。  結果就是暫停資料庫。 最終的命令會顯示結果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>啟動資料倉儲示例

繼續 "Server01" 伺服器上託管之 "Database02" 資料庫的作業。 此伺服器包含在「ResourceGroup1」這個資源群組中。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

一種變化，此範例會從 "ResourceGroup1" 資源群組包含的 "Server01" 伺服器中，擷取 "Database02" 資料庫。 它將檢索到的物件管道到[恢復-AzSql資料庫](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> 請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為 PowerShell Cmdlet 中的 -ServerName。
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>其他支援的 PowerShell Cmdlet
Azure 同步分析資料倉庫支援這些 PowerShell Cmdlet。

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [獲取 AzSql 資料庫還原點](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [新-AzSql 資料庫](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [恢復-AzSql 資料庫](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [選擇-訂閱](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [暫停-AzSql 資料庫](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>後續步驟
如需更多 PowerShell 範例，請參閱：

* [使用 PowerShell 創建資料倉儲](create-data-warehouse-powershell.md)
* [資料庫還原](sql-data-warehouse-restore-points.md)

有關可以使用 PowerShell 自動執行的其他任務，請參閱[Azure SQL 資料庫 Cmdlet](https://docs.microsoft.com/powershell/module/az.sql)。 並非所有 Azure Sql 資料庫 Cmdlet 都支援 Azure 同步分析資料倉庫。  有關可以使用 REST 自動執行的工作清單，請參閱 Azure [SQL 資料庫的操作](/rest/api/sql/)。
