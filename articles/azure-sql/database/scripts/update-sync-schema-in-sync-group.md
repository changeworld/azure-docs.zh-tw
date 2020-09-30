---
title: PowerShell：更新 SQL 資料同步的同步結構描述
description: 更新 SQL 資料同步之同步結構描述的 Azure PowerShell 範例指令碼
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c60bff122b6ad9bef4e9e4558d9858e1243ebd1f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321436"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>使用 PowerShell 更新現有同步群組中的同步結構描述
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

此 Azure PowerShell 範例會更新現有「SQL 資料同步」同步群組中的同步結構描述。 當您要同步處理多個資料表時，此指令碼可協助您有效率地更新同步結構描述。 此範例會示範 **UpdateSyncSchema** 指令碼的使用方式，這個指令碼在 GitHub 上會以 [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) 提供使用。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果選擇在本機安裝並使用 PowerShell，此教學課程需要 Az PowerShell 1.4.0 或更新版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

如需 SQL 資料同步的概觀，請參閱[使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步處理資料](../sql-data-sync-data-sql-server-sql-database.md)。

> [!IMPORTANT]
> SQL 資料同步目前不支援 Azure SQL 受控執行個體。

## <a name="examples"></a>範例

### <a name="add-all-tables-to-the-sync-schema"></a>將所有的資料表新增至同步結構描述

下列範例會重新整理資料庫結構描述，並將中樞資料庫中所有有效的資料表，新增至同步結構描述。

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>新增和移除資料表和資料行

下列範例會將 `[dbo].[Table1]` 和 `[dbo].[Table2].[Column1]` 新增至同步結構描述，並移除 `[dbo].[Table3]`。

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>指令碼參數

**UpdateSyncSchema** 指令碼具有下列參數︰

| 參數 | 注意 |
|---|---|
| $subscriptionId | 其中建立同步群組的訂閱。 |
| $resourceGroupName | 其中建立同步群組的資源群組。|
| $serverName | 中樞資料庫的伺服器名稱。|
| $databaseName | 中樞資料庫名稱。 |
| $syncGroupName | 同步群組名稱。 |
| $memberName | 如果您希望從同步成員而非從中樞資料庫載入資料庫結構描述，請指定成員名稱。 如果您希望從中樞資料庫載入資料庫結構描述，請將此參數保留空白。 |
| $timeoutInSeconds | 當指令碼重新整理資料庫結構描述時等候逾時。 預設值為 900 秒。 |
| $refreshDatabaseSchema | 指定指令碼是否需要重新整理資料庫結構描述。 如果您的資料庫結構描述與先前的設定有所變更 (例如，如果您已新增新的資料表或新的資料行)，則必須重新整理結構描述，才能加以重新設定。 預設值為 false。 |
| $addAllTables | 如果此值為 true，所有有效的資料表和資料行會新增到同步結構描述中。 $TablesAndColumnsToAdd and $TablesAndColumnsToRemove 的值會被忽略。 |
| $tablesAndColumnsToAdd | 指定要新增到同步結構描述中的資料表或資料行。 每個資料表或資料行名稱必須使用分隔符號與結構描述名稱完全分隔。 例如：`[dbo].[Table1]`, `[dbo].[Table2].[Column1]`。 可以指定多個資料表或資料行名稱，並以逗號 (,) 分隔。 |
| $tablesAndColumnsToRemove | 指定要從同步結構描述中移除的資料表或資料行。 每個資料表或資料行名稱必須使用分隔符號與結構描述名稱完全分隔。 例如：`[dbo].[Table1]`, `[dbo].[Table2].[Column1]`。 可以指定多個資料表或資料行名稱，並以逗號 (,) 分隔。 |

## <a name="script-explanation"></a>指令碼說明

**UpdateSyncSchema** 指令碼具有下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | 傳回有關同步群組的資訊。 |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | 更新同步群組。 |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | 傳回有關同步成員的資訊。 |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | 傳回有關同步結構描述的資訊。 |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | 更新同步結構描述。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure SQL Database PowerShell 指令碼](../powershell-script-content-guide.md)中找到其他 SQL Database PowerShell 指令碼範例。

如需有關 SQL 資料同步的詳細資訊，請參閱：

- 概觀 - [使用 Azure 中的 SQL 資料同步以同步處理 Azure SQL Database 與 SQL Server 之間的資料](../sql-data-sync-data-sql-server-sql-database.md)
- 設定資料同步
    - 使用 Azure 入口網站 - [教學課程：設定 SQL 資料同步以同步處理 Azure SQL Database 與 SQL Server 之間的資料](../sql-data-sync-sql-server-configure.md)
    - 使用 PowerShell
        -  [使用 PowerShell 同步處理 Azure SQL Database 中多個資料庫之間的資料](sql-data-sync-sync-data-between-sql-databases.md)
        -  [使用 PowerShell 同步處理 Azure SQL Database 與 SQL Server 之間的資料](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent - [適用於 Azure 中 SQL 資料同步的 Data Sync Agent](../sql-data-sync-agent-overview.md)
- 最佳做法 - [Azure 中的 SQL 資料同步最佳做法](../sql-data-sync-best-practices.md)
- 監視 - [使用 Azure 監視器記錄監視 SQL 資料同步](../sql-data-sync-monitor-sync.md)
- 疑難排解 - [在 Azure 中針對 SQL 資料同步的問題進行疑難排解](../sql-data-sync-troubleshoot.md)
- 更新同步結構描述
    - 使用 Transact-SQL - [在 Azure 中將 SQL 資料同步結構描述變更的複寫自動化](../sql-data-sync-update-sync-schema.md)

如需有關 SQL Database 的詳細資訊，請參閱：

- [SQL Database 概觀](../sql-database-paas-overview.md)
- [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
