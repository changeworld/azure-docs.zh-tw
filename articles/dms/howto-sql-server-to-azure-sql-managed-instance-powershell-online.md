---
title: PowerShell：將 SQL Server 遷移至 SQL 受控執行個體 online
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 Azure PowerShell 和 Azure 資料庫移轉服務，從 SQL Server 線上遷移至 Azure SQL 受控執行個體。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697779"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>使用 PowerShell & Azure 資料庫移轉服務，將 SQL Server 遷移至 SQL 受控執行個體 online

在本文中，您會使用 Microsoft Azure PowerShell，線上將還原至 SQL Server 2005 或更新版本的內部部署實例的 **Adventureworks2016** 資料庫移轉至 Azure sql 受控執行個體。 您可以使用 Microsoft Azure PowerShell 中的模組，將資料庫從 SQL Server 實例遷移至 SQL 受控執行個體 `Az.DataMigration` 。

在本文中，您將學會如何：
> [!div class="checklist"]
>
> * 建立資源群組。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 在 Azure 資料庫移轉服務的實例中建立遷移專案。
> * 執行線上遷移。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文提供線上遷移的步驟，但也可以 [離線](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)遷移。


## <a name="prerequisites"></a>必要條件

若要完成這些步驟，您需要：

* [SQL Server 2016 或](https://www.microsoft.com/sql-server/sql-server-downloads) 更新版本 (任何版本) 。
* **AdventureWorks2016** 資料庫的本機複本，可在 [這裡](/sql/samples/adventureworks-install-configure)下載。
* 啟用 SQL Server Express 安裝預設已停用的 TCP/IP 通訊協定。 依照[啟用或停用伺服器網路通訊協定](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文的說明，啟用 TCP/IP 通訊協定。
* 設定[用於 Database Engine 存取的 Windows 防火牆](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
* SQL 受控執行個體。 您可以遵循 [建立 >script.asql 受控執行個體](../azure-sql/managed-instance/instance-create-quickstart.md)一文中的詳細資料來建立 SQL 受控執行個體。
* 下載並安裝 [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3 版或更新版本。
* 使用 Azure Resource Manager 部署模型建立的 Microsoft Azure 虛擬網路，其會使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)，為 Azure 資料庫移轉服務提供對內部部署來源伺服器的站對站連線能力。
* 使用 Data Migration Assistant 完成內部部署資料庫和架構遷移的評估，如 [執行 SQL Server 遷移評估](/sql/dma/dma-assesssqlonprem)一文所述。
* 若要下載並安裝 `Az.DataMigration` 模組 (版本0.7.2 版或更新版本，請使用 [Install 模組 PowerShell Cmdlet](/powershell/module/powershellget/Install-Module)從 PowerShell 資源庫) 。
* 若要確保用來連接到來源 SQL Server 實例的認證具有 [CONTROL Server](/sql/t-sql/statements/grant-server-permissions-transact-sql) 許可權。
* 若要確保用來連接到目標 SQL 受控執行個體的認證具有目標 SQL 受控執行個體資料庫的 CONTROL DATABASE 許可權。

    > [!IMPORTANT]
    > 針對線上遷移，您必須已經設定 Azure Active Directory 認證。 如需詳細資訊，請參閱 [使用入口網站來建立可存取資源 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)的文章。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用命令建立資源群組 [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) 。

下列範例會在 *美國東部* 區域建立名為 *myResourceGroup* 的資源群組。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>建立 DMS 的實例

您可以使用 `New-AzDataMigrationService` Cmdlet，來建立新的 Azure 資料庫移轉服務執行個體。
此 Cmdlet 預期有下列必要參數：

* *Azure 資源組名*。 您可以使用 [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) 命令來建立 Azure 資源群組（如先前所示），並將其名稱提供為參數。
* *服務名稱*。 字串，對應至 Azure 資料庫移轉服務所需的唯一服務名稱。
* *位置*。 指定服務的位置。 指定 Azure 資料中心位置，例如美國西部或東南亞。
* *Sku*。 此參數會對應至 DMS SKU 名稱。 目前支援的 Sku 名稱為 *Basic_1vCore*、 *Basic_2vCores* *GeneralPurpose_4vCores*。
* 虛擬子網路識別碼。 您可以使用 Cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 來建立子網。

下列範例會使用名為 *MyVNET* 的虛擬網路和名為 *>mysubnet* 的子網，在位於 *美國東部* 區域的資源群組 *>mydms* 中建立名為 *>mydmsresourcegroup* 的服務。


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>建立移轉專案

建立 Azure 資料庫移轉服務執行個體之後，建立移轉專案。 Azure 資料庫移轉服務專案需要來源和目標執行個體的連線資訊，以及您想要移轉的資料庫清單。
定義來源和目標連接連接字串。

下列腳本會定義來源 SQL Server 連接詳細資料： 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

下列腳本會定義目標 SQL 受控執行個體連接詳細資料： 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>定義來源和目標資料庫對應

提供要在此遷移專案中遷移的資料庫

下列腳本會將源資料庫對應至目標 SQL 受控執行個體上具有所提供名稱的個別新資料庫。

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

針對多個資料庫，請使用下列格式，將資料庫清單新增至上述腳本：

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>建立 DMS 專案

您可以在 DMS 實例內建立 Azure 資料庫移轉服務專案。

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>建立備份檔案共用物件

現在建立檔案共用物件，此物件代表 Azure 資料庫移轉服務可以使用 New-AzDmsFileShare Cmdlet 進行源資料庫備份的本機 SMB 網路共用。

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>定義 Azure 儲存體 

選取要用於遷移的 Azure 儲存體容器： 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>設定 Azure Active Directory 應用程式

提供線上 SQL 受控執行個體遷移的 Azure Active Directory 所需的詳細資料： 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>建立並啟動移轉工作

接著，建立並啟動 Azure 資料庫移轉服務工作。 使用變數呼叫來源和目標，並列出要遷移的資料庫資料表： 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

下列範例會建立並啟動線上遷移工作： 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

如需詳細資訊，請參閱 [AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask)。

## <a name="monitor-the-migration"></a>監視移轉

若要監視遷移作業，請執行下列工作。

### <a name="check-the-status-of-task"></a>檢查工作的狀態

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

使用下列各項來取得錯誤清單：-

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>執行轉換 

在線上遷移中，會執行資料庫的完整備份和還原作業，然後繼續進行，以還原儲存在 BackupFileShare 中的交易記錄。

當 Azure SQL 受控執行個體中的資料庫以最新的資料更新，而且與源資料庫同步時，您可以執行轉換。

下列範例將完成 cutover\migration。 使用者自行叫用此命令。

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>正在刪除 Azure 資料庫移轉服務的實例

完成遷移之後，您可以刪除 Azure 資料庫移轉服務實例：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>其他資源

如需其他遷移案例 (來源/目標群組) 的詳細資訊，請參閱 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/)。

## <a name="next-steps"></a>後續步驟

在「 [什麼是 Azure 資料庫移轉服務](./dms-overview.md)」一文中，深入瞭解 Azure 資料庫移轉服務。