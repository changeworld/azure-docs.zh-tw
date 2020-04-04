---
title: 電源外殼& REST API
description: 查找 Azure Synapse 分析 SQL 池的頂級 PowerShell cmdlet,包括如何暫停和恢復資料庫。
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
ms.openlocfilehash: 644ad931879c1953560abf13ac0eda58f3d814d8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633068"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>適用於 Azure 突觸分析 SQL 池的 PowerShell & REST API

許多 Azure 突觸分析 SQL 池管理任務可以使用 Azure PowerShell cmdlet 或 REST API 進行管理。  下面是如何使用 PowerShell 命令自動執行 SQL 池中常見任務的一些示例。  如需一些良好的 REST 範例，請參閱[使用 REST 管理延展性](sql-data-warehouse-manage-compute-rest-api.md)一文。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>開始使用 Azure PowerShell Cmdlet

1. 開啟 Windows PowerShell。
2. 在 PowerShell 提示中，執行下列命令來登入 Azure Resource Manager，並選取您的訂用帳戶。

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>暫停資料主目錄範例

暫停 "Server01" 伺服器上託管的 "Database02" 資料庫。  此伺服器位於「ResourceGroup1」這個 Azure 資源群組。

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

變數,此表示例將檢索到的物件導管到[掛起-AzSql 資料庫](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)。  結果就是暫停資料庫。 最終的命令會顯示結果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>啟動資料主目錄範例

繼續 "Server01" 伺服器上託管之 "Database02" 資料庫的作業。 此伺服器包含在「ResourceGroup1」這個資源群組中。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

一種變化，此範例會從 "ResourceGroup1" 資源群組包含的 "Server01" 伺服器中，擷取 "Database02" 資料庫。 它將檢索到的物件導管到[復原-AzSql資料庫](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> 請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為 PowerShell Cmdlet 中的 -ServerName。

## <a name="other-supported-powershell-cmdlets"></a>其他支援的 PowerShell Cmdlet

Azure 同步分析數據倉庫支援這些 PowerShell cmdlet。

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [取得 AzSql 資料庫還原點](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [新-AzSql 資料庫](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [還原-AzSql資料庫]/電源外殼/模組/az.sql/還原-azsql数据庫?toc_/azure/突觸分析/sql-data倉庫/toc.json&bc_/azure/同步分析/sql-數據倉庫/麵包屑/toc.json)
* [復原-AzSql 資料庫](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [暫停-AzSql 資料庫](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>後續步驟

如需更多 PowerShell 範例，請參閱：

* [使用 PowerShell 建立資料倉儲](create-data-warehouse-powershell.md)
* [資料庫還原](sql-data-warehouse-restore-points.md)

有關可以使用 PowerShell 自動實現的其他任務,請參閱 [Azure SQL 資料庫 cmdlet]/電源殼/模組/az.sql?toc_/azure/synapse 分析/sql-數據倉庫/toc.json&bc_/azure/同步分析/sql-data-倉庫/麵包屑/toc.json)。 並非所有 Azure Sql 資料庫 cmdlet 都支援 Azure 同步分析數據倉庫。 有關可以使用 REST 自動執行的工作清單,請參閱 Azure [SQL 資料庫的操作](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
