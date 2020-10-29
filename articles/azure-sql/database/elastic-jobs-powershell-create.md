---
title: 使用 PowerShell 建立彈性作業代理程式 (預覽)
description: 了解如何使用 PowerShell 建立彈性作業代理程式。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 10/21/2020
ms.openlocfilehash: 27cd35eba7320022ea9b137a7b8bb079a1226751
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427297"
---
# <a name="create-an-elastic-job-agent-using-powershell-preview"></a>使用 PowerShell 建立彈性作業代理程式 (預覽)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[彈性作業 (預覽)](job-automation-overview.md#elastic-database-jobs-preview) 可讓您以平行方式跨多個資料庫執行一或多個 Transact-SQL (T-SQL) 指令碼。

在本教學課程中，您將了解跨多個資料庫執行查詢所需的步驟：

> [!div class="checklist"]
> * 建立彈性工作代理程式
> * 建立作業認證，讓作業可在其目標上執行指令碼
> * 定義您要對其執行作業的目標 (伺服器、彈性集區、資料庫、分區對應)
> * 在目標資料庫中建立資料庫範圍認證，讓代理程式可連接及執行作業
> * 建立作業
> * 將作業步驟新增至作業
> * 開始執行作業
> * 監視作業

## <a name="prerequisites"></a>必要條件

升級版本的彈性資料庫作業具有一組新的 PowerShell Cmdlet，可以在移轉期間使用。 這些新的 Cmdlet 會將您所有的現有作業認證、目標 (包括資料庫、伺服器、自訂集合)、作業觸發程序、作業排程、作業內容及作業傳輸至新的彈性資料庫代理程式。

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>安裝最新的彈性作業 Cmdlet

如果您還沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

安裝 **Az.Sql** 模組，以取得最新的彈性作業 Cmdlet。 在 PowerShell 中以系統管理存取權執行下列命令。

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

除了 **Az.Sql** 模組以外，本教學課程也需要 *SqlServer* PowerShell 模組。 如需詳細資訊，請參閱[安裝 SQL Server PowerShell 模組](/sql/powershell/download-sql-server-ps-module)。

## <a name="create-required-resources"></a>建立所需的資源

要建立彈性作業代理程式，必須要有作為[作業資料庫](job-automation-overview.md#job-database)的資料庫 (S0 或更高版本)。

下列指令碼會建立新的資源群組、伺服器，以及作為作業資料庫的資料庫。 第二個指令碼會建立含有兩個空白資料庫的第二個伺服器，以對其執行作業。

彈性作業沒有特定的命名需求，因此，您可以使用您所需的任何命名慣例，只要它們符合 [Azure 需求](/azure/architecture/best-practices/resource-naming)即可。

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

## <a name="use-elastic-jobs"></a>使用彈性作業

若要使用彈性作業，請執行下列命令以在 Azure 訂用帳戶中註冊此功能。 針對要在其中佈建彈性作業代理程式的訂用帳戶執行此命令一次。 訂用帳戶若只包含屬於作業目標的資料庫就不需要註冊。

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>建立彈性作業代理程式

彈性作業代理程式是用來建立、執行和管理作業的 Azure 資源。 代理程式會根據排程來執行作業，或執行一次性的作業。

**New-AzSqlElasticJobAgent** Cmdlet 會要求 Azure SQL Database 中必須已有資料庫存在，因此 *resourceGroupName* 、 *rerverName* 與 *databaseName* 參數全都必須指向現有的資源。

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>建立作業認證

作業會在執行時使用資料庫範圍的認證來連線至目標群組所指定的目標資料庫，然後執行指令碼。 這些資料庫範圍認證也可用來連線至主要資料庫，以在伺服器或彈性集區中的資料庫作為目標群組成員類型時，將這些資料庫全數列舉。

資料庫範圍認證必須建立在作業資料庫中。 所有目標資料庫都必須以足夠的權限登入，作業才能順利完成。

![彈性作業認證](./media/elastic-jobs-powershell-create/job-credentials.png)

除了影像中的認證以外，請留意在下列指令碼中新增的 **GRANT** 命令。 我們為此範例作業選擇的指令碼需要這些權限。 由於此範例會在目標資料庫中建立新的資料表，因此每個目標資料庫都必須具備適當的權限才能成功執行。

若要建立必要的作業認證 (在作業資料庫中)，請執行下列指令碼：

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = 'CREATE LOGIN masteruser WITH PASSWORD=''password!123'''
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = 'CREATE LOGIN jobuser WITH PASSWORD=''password!123'''
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String 'password!123' -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>定義要對其執行作業的目標資料庫

[目標群組](job-automation-overview.md#target-group)可定義一或多個將會執行作業步驟的資料庫。

下列程式碼片段會建立兩個目標群組： *serverGroup* 和 *serverGroupExcludingDb2* 。 *serverGroup* 會以執行時存在於伺服器上的所有資料庫為目標， *serverGroupExcludingDb2* 則會以伺服器上的所有資料庫為目標，但 *targetDb2* 除外：

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>建立作業和步驟

此範例會為要執行的作業定義一項作業和兩個作業步驟。 第一個作業步驟 ( *步驟 1* ) 會在目標群組 *ServerGroup* 的每個資料庫中 建立新的資料表 ( *Step1Table* )。 第二個作業步驟 ( *步驟 2* ) 會在每個資料庫中建立新的資料表 ( *Step2Table* )，但 *TargetDb2* 除外，因為先前定義的目標群組指定要加以排除。

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>執行作業

若要立即啟動作業，請執行下列命令：

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

順利完成之後，您應該會在 TargetDb1 中看到兩個新的資料表，而 TargetDb2 中只有一個新的資料表：

   ![SSMS 中的新資料表驗證](./media/elastic-jobs-powershell-create/job-execution-verification.png)

您也可以排程要稍後執行的作業。 若要將作業排程在特定時間執行，請執行下列命令：

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>監視作業執行狀態

下列程式碼片段會取得作業執行詳細資料：

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

下表列出可能的作業執行狀態：

|State|描述|
|:---|:---|
|**建立日期** | 作業執行剛建立，且尚未開始執行。|
|**InProgress** | 作業執行目前正在進行中。|
|**WaitingForRetry** | 作業執行無法完成其動作，且正在等候重試。|
|**已成功** | 作業執行已順利完成。|
|**SucceededWithSkipped** | 作業執行已順利完成，但略過了部分子系。|
|**已失敗** | 作業執行失敗，且用完重試次數。|
|**TimedOut** | 作業執行逾時。|
|**Canceled** | 作業執行已取消。|
|**已略過** | 已略過作業執行，因為已在相同的目標上執行相同作業步驟的另一個執行。|
|**WaitingForChildJobExecutions** | 作業執行正在等候其子系執行完成。|

## <a name="clean-up-resources"></a>清除資源

您可以刪除資源群組，以刪除在本教學課程中建立的 Azure 資源。

> [!TIP]
> 如果您打算繼續使用這些作業，請勿清除在此本文中建立的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已對一組資料庫執行 Transact-SQL 指令碼。 您已了解如何執行下列工作：

> [!div class="checklist"]
> * 建立彈性工作代理程式
> * 建立作業認證，讓作業可在其目標上執行指令碼
> * 定義您要對其執行作業的目標 (伺服器、彈性集區、資料庫、分區對應)
> * 在目標資料庫中建立資料庫範圍認證，讓代理程式可連接及執行作業
> * 建立作業
> * 將作業步驟新增至作業
> * 開始執行作業
> * 監視作業

> [!div class="nextstepaction"]
> [使用 Transact-SQL 管理彈性作業](elastic-jobs-tsql-create-manage.md)
