---
title: PowerShell：同步處理 SQL Database 與 SQL Server 之間的資料
description: 使用 Azure PowerShell 範例指令碼同步處理 Azure SQL Database 與 SQL Server 之間的資料。
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
ms.openlocfilehash: 194c2466e29c15ac35cf0bd88b90bf9f816e9035
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284426"
---
# <a name="use-powershell-to-sync-data-between-sql-database-and-sql-server"></a>使用 PowerShell 同步處理 SQL Database 與 SQL Server 之間的資料

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

此 Azure PowerShell 範例指令碼會設定資料同步，以同步處理 Azure SQL Database 與 SQL Server 之間的資料。 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果選擇在本機安裝並使用 PowerShell，此教學課程需要 Az PowerShell 1.4.0 或更新版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

如需 SQL 資料同步的概觀，請參閱[在 Azure 中使用 SQL 資料同步，跨多個雲端與內部部署資料庫同步處理資料](../sql-data-sync-data-sql-server-sql-database.md)。

> [!IMPORTANT]
> SQL 資料同步目前不支援 Azure SQL 受控執行個體。

## <a name="prerequisites"></a>必要條件

- 在 Azure SQL Database 中從 AdventureWorksLT 範例資料庫建立資料庫，以作為中樞資料庫。
- 在與同步資料庫相同的區域中，於 Azure SQL Database 中建立資料庫。
- 在 SQL Server 執行個體中建立資料庫，以作為成員資料庫。
- 在執行範例之前更新參數預留位置。

## <a name="example"></a>範例

```powershell-interactive
using namespace Microsoft.Azure.Commands.Sql.DataSync.Model
using namespace System.Collections.Generic

# hub database info
$subscriptionId = "<subscriptionId>"
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$databaseName = "<databaseName>"

# sync database info
$syncDatabaseResourceGroupName = "<syncResourceGroupName>"
$syncDatabaseServerName = "<syncServerName>"
$syncDatabaseName = "<syncDatabaseName>"

# sync group info
$syncGroupName = "<syncGroupName>"
$conflictResolutionPolicy = "HubWin" # can be HubWin or MemberWin
$intervalInSeconds = 300 # sync interval in seconds (must be no less than 300)

# member database info
$syncMemberName = "<syncMemberName>"
$memberServerName = "<memberServerName>"
$memberDatabaseName = "<memberDatabaseName>"
$memberDatabaseType = "SqlServerDatabase" # can be AzureSqlDatabase or SqlServerDatabase
$syncDirection = "Bidirectional" # can be Bidirectional, Onewaymembertohub, Onewayhubtomember

# sync agent info
$syncAgentName = "<agentName>"
$syncAgentResourceGroupName = "<syncAgentResourceGroupName>"
$syncAgentServerName = "<syncAgentServerName>"

# temp file to save the sync schema
$tempFile = $env:TEMP+"\syncSchema.json"

# list of included columns and tables in quoted name
$includedColumnsAndTables =  "[SalesLT].[Address].[AddressID]",
                             "[SalesLT].[Address].[AddressLine2]",
                             "[SalesLT].[Address].[rowguid]",
                             "[SalesLT].[Address].[PostalCode]",
                             "[SalesLT].[ProductDescription]"
$metadataList = [System.Collections.ArrayList]::new($includedColumnsAndTables)

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

# use if it's safe to show password in script, otherwise use PromptForCredential
# $user = "username"
# $password = ConvertTo-SecureString -String "password" -AsPlainText -Force
# $credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $user, $password

$credential = $Host.ui.PromptForCredential("Need credential",
              "Please enter your user name and password for server "+$serverName+".database.windows.net",
              "",
              "")

# create a new sync agent
Write-Host "Creating new Sync Agent..."
New-AzSqlSyncAgent -ResourceGroupName $resourceGroupName -ServerName  $serverName -SyncDatabaseName $syncDatabaseName -SyncAgentName $syncAgentName

# generate agent key
Write-Host "Generating Agent Key..."
$agentKey = New-AzSqlSyncAgentKey -ResourceGroupName $resourceGroupName -ServerName $serverName -SyncAgentName $syncAgentName
Write-Host "Use your agent key to configure the sync agent. Do this before proceeding."
$agentkey

# DO THE FOLLOWING BEFORE THE NEXT STEP
# Install the on-premises sync agent on your machine and register the sync agent using the agent key generated above to bring the sync agent online.
# Add the SQL Server database information including server name, database name, user name, password on the configuration tool within the sync agent.  

# create a new sync group
Write-Host "Creating Sync Group "$syncGroupName"..."
New-AzSqlSyncGroup -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Name $syncGroupName `
    -SyncDatabaseName $syncDatabaseName -SyncDatabaseServerName $syncDatabaseServerName -SyncDatabaseResourceGroupName $syncDatabaseResourceGroupName `
    -ConflictResolutionPolicy $conflictResolutionPolicy -DatabaseCredential $credential

# use if it's safe to show password in script, otherwise use PromptForCredential
#$user = "username"
#$password = ConvertTo-SecureString -String "password" -AsPlainText -Force
#$credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $user, $password

$credential = $Host.ui.PromptForCredential("Need credential",
              "Please enter your user name and password for server "+$memberServerName,
              "",
              "")

# get information from sync agent and confirm your SQL Server instance was configured (note the database ID to use for the sqlServerDatabaseID in the next step)
$syncAgentInfo = Get-AzSqlSyncAgentLinkedDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -SyncAgentName $syncAgentName

# add a new sync member
Write-Host "Adding member"$syncMemberName" to the sync group..."

New-AzSqlSyncMember -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
    -SyncGroupName $syncGroupName -Name $syncMemberName -MemberDatabaseType $memberDatabaseType -SyncAgentResourceGroupName $syncAgentResourceGroupName `
    -SyncAgentServerName $syncAgentServerName -SyncAgentName $syncAgentName  -SyncDirection $syncDirection -SqlServerDatabaseID  $syncAgentInfo.DatabaseId

# refresh database schema from hub database, specify the -SyncMemberName parameter if you want to refresh schema from the member database
Write-Host "Refreshing database schema from hub database..."
$startTime = Get-Date
Update-AzSqlSyncSchema -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -SyncGroupName $syncGroupName

# waiting for successful refresh
$startTime = $startTime.ToUniversalTime()
$timer=0
$timeout=90

# check the log and see if refresh has gone through
Write-Host "Check for successful refresh..."
$isSucceeded = $false
while ($isSucceeded -eq $false) {
    Start-Sleep -s 10
    $timer=$timer+10
    $details = Get-AzSqlSyncSchema -SyncGroupName $syncGroupName -ServerName $serverName -DatabaseName $databaseName -ResourceGroupName $resourceGroupName
    if ($details.LastUpdateTime -gt $startTime) {
        Write-Host "Refresh was successful"
        $isSucceeded = $true
    }
    if ($timer -eq $timeout) {
        Write-Host "Refresh timed out"
        break;
    }
}

# get the database schema
Write-Host "Adding tables and columns to the sync schema..."
$databaseSchema = Get-AzSqlSyncSchema -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
    -DatabaseName $DatabaseName -SyncGroupName $SyncGroupName `

$databaseSchema | ConvertTo-Json -depth 5 -Compress | Out-File "C:\Users\OnPremiseServer\AppData\Local\Temp\syncSchema.json"

$newSchema = [AzureSqlSyncGroupSchemaModel]::new()
$newSchema.Tables = [List[AzureSqlSyncGroupSchemaTableModel]]::new();

# add columns and tables to the sync schema
foreach ($tableSchema in $databaseSchema.Tables) {
    $newTableSchema = [AzureSqlSyncGroupSchemaTableModel]::new()
    $newTableSchema.QuotedName = $tableSchema.QuotedName
    $newTableSchema.Columns = [List[AzureSqlSyncGroupSchemaColumnModel]]::new();
    $addAllColumns = $false
    if ($MetadataList.Contains($tableSchema.QuotedName)) {
        if ($tableSchema.HasError) {
            $fullTableName = $tableSchema.QuotedName
            Write-Host "Can't add table $fullTableName to the sync schema" -foregroundcolor "Red"
            Write-Host $tableSchema.ErrorId -foregroundcolor "Red"
            continue;
        }
        else {
            $addAllColumns = $true
        }
    }
    foreach($columnSchema in $tableSchema.Columns) {
        $fullColumnName = $tableSchema.QuotedName + "." + $columnSchema.QuotedName
        if ($addAllColumns -or $MetadataList.Contains($fullColumnName)) {
            if ((-not $addAllColumns) -and $tableSchema.HasError) {
                Write-Host "Can't add column $fullColumnName to the sync schema" -foregroundcolor "Red"
                Write-Host $tableSchema.ErrorId -foregroundcolor "Red"
            }
            elseif ((-not $addAllColumns) -and $columnSchema.HasError) {
                Write-Host "Can't add column $fullColumnName to the sync schema" -foregroundcolor "Red"
                Write-Host $columnSchema.ErrorId -foregroundcolor "Red"
            }
            else {
                Write-Host "Adding"$fullColumnName" to the sync schema"
                $newColumnSchema = [AzureSqlSyncGroupSchemaColumnModel]::new()
                $newColumnSchema.QuotedName = $columnSchema.QuotedName
                $newColumnSchema.DataSize = $columnSchema.DataSize
                $newColumnSchema.DataType = $columnSchema.DataType
                $newTableSchema.Columns.Add($newColumnSchema)
            }
        }
    }
    if ($newTableSchema.Columns.Count -gt 0) {
        $newSchema.Tables.Add($newTableSchema)
    }
}

# convert sync schema to JSON format
$schemaString = $newSchema | ConvertTo-Json -depth 5 -Compress

# workaround a powershell bug
$schemaString = $schemaString.Replace('"Tables"', '"tables"').Replace('"Columns"', '"columns"').Replace('"QuotedName"', '"quotedName"').Replace('"MasterSyncMemberName"','"masterSyncMemberName"')

# save the sync schema to a temp file
$schemaString | Out-File $tempFile

# update sync schema
Write-Host "Updating the sync schema..."
Update-AzSqlSyncGroup -ResourceGroupName $resourceGroupName -ServerName $serverName `
    -DatabaseName $databaseName -Name $syncGroupName -Schema $tempFile

$syncLogStartTime = Get-Date

# trigger sync manually
Write-Host "Trigger sync manually..."
Start-AzSqlSyncGroupSync -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -SyncGroupName $syncGroupName

# check the sync log and wait until the first sync succeeded
Write-Host "Check the sync log..."
$isSucceeded = $false
for ($i = 0; ($i -lt 300) -and (-not $isSucceeded); $i = $i + 10) {
    Start-Sleep -s 10
    $syncLogEndTime = Get-Date
    $syncLogList = Get-AzSqlSyncGroupLog -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -SyncGroupName $syncGroupName -StartTime $syncLogStartTime.ToUniversalTime() -EndTime $syncLogEndTime.ToUniversalTime()

    if ($synclogList.Length -gt 0) {
        foreach ($syncLog in $syncLogList) {
            if ($syncLog.Details.Contains("Sync completed successfully")) {
                Write-Host $syncLog.TimeStamp : $syncLog.Details
                $isSucceeded = $true
            }
        }
    }
}

if ($isSucceeded) {
    # enable scheduled sync
    Write-Host "Enable the scheduled sync with 300 seconds interval..."
    Update-AzSqlSyncGroup  -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -Name $syncGroupName -IntervalInSeconds $intervalInSeconds
}
else {
    # output all log if sync doesn't succeed in 300 seconds
    $syncLogEndTime = Get-Date
    $syncLogList = Get-AzSqlSyncGroupLog  -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -SyncGroupName $syncGroupName -StartTime $syncLogStartTime.ToUniversalTime() -EndTime $syncLogEndTime.ToUniversalTime()

    if ($synclogList.Length -gt 0) {
        foreach ($syncLog in $syncLogList) {
            Write-Host $syncLog.TimeStamp : $syncLog.Details
        }
    }
}
```

## <a name="clean-up-deployment"></a>清除部署

執行範例指令碼之後，您可以執行下列命令以移除資源群組及與其相關聯的所有資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Remove-AzResourceGroup -ResourceGroupName $syncDatabaseResourceGroupName
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzSqlSyncAgent](/powershell/module/az.sql/New-azSqlSyncAgent) |  建立新的同步代理程式。 |
| [New-AzSqlSyncAgentKey](/powershell/module/az.sql/New-azSqlSyncAgentKey) |  產生與同步代理程式相關聯的代理程式金鑰。 |
| [Get-AzSqlSyncAgentLinkedDatabase](/powershell/module/az.sql/Get-azSqlSyncAgentLinkedDatabase) |  取得同步代理程式的所有資訊。 |
| [New-AzSqlSyncMember](/powershell/module/az.sql/New-azSqlSyncMember) |  將新成員新增至同步群組。 |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/Update-azSqlSyncSchema) |  重新整理資料庫結構描述資訊。 |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/Get-azSqlSyncSchema) |  取得資料庫結構描述資訊。 |
| [Update-AzSqlSyncGroup](/powershell/module/az.sql/Update-azSqlSyncGroup) |  更新同步群組。 |
| [Start-AzSqlSyncGroupSync](/powershell/module/az.sql/Start-azSqlSyncGroupSync) | 觸發同步處理。 |
| [Get-AzSqlSyncGroupLog](/powershell/module/az.sql/Get-azSqlSyncGroupLog) |  檢查同步記錄。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure SQL Database PowerShell 指令碼](../powershell-script-content-guide.md)中找到其他 SQL Database PowerShell 指令碼範例。

如需有關 SQL 資料同步的詳細資訊，請參閱：

- 概觀 - [使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步資料](../sql-data-sync-data-sql-server-sql-database.md)
- 設定資料同步
    - 使用 Azure 入口網站 - [教學課程：設定 SQL 資料同步以同步處理 Azure SQL Database 中的資料庫與 SQL Server 內部部署資料庫之間的資料](../sql-data-sync-sql-server-configure.md)
    - 使用 PowerShell - [使用 PowerShell 在 Azure SQL Database 中的多個資料庫之間進行同步處理](sql-data-sync-sync-data-between-sql-databases.md)
- Data Sync Agent - [適用於 Azure 中 SQL 資料同步的 Data Sync Agent](../sql-data-sync-agent-overview.md)
- 最佳做法 - [Azure 中的 SQL 資料同步最佳做法](../sql-data-sync-best-practices.md)
- 監視 - [使用 Azure 監視器記錄監視 SQL 資料同步](../sql-data-sync-monitor-sync.md)
- 疑難排解 - [在 Azure 中針對 SQL 資料同步的問題進行疑難排解](../sql-data-sync-troubleshoot.md)
- 更新同步結構描述
    - 使用 Transact-SQL - [在 Azure 中將 SQL 資料同步結構描述變更的複寫自動化](../sql-data-sync-update-sync-schema.md)
    - 使用 PowerShell - [使用 PowerShell 更新現有同步群組中的同步結構描述](update-sync-schema-in-sync-group.md)

如需有關 Azure SQL Database 的詳細資訊，請參閱：

- [SQL Database 概觀](../sql-database-paas-overview.md)
- [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
