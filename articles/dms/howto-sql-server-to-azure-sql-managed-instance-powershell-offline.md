---
title: PowerShell：將 SQL Server 遷移至 SQL 受控執行個體離線
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 Azure PowerShell 和 Azure 資料庫移轉服務，從 SQL Server 離線遷移至 Azure SQL 受控執行個體。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 90663b6beb4f1e3f7ade32e603a53c8b9d9158f5
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697786"
---
# <a name="migrate-sql-server-to-sql-managed-instance-offline-with-powershell--azure-database-migration-service"></a>使用 PowerShell & Azure 資料庫移轉服務，將 SQL Server 遷移至 SQL 受控執行個體離線

在本文中，您會使用 Microsoft Azure PowerShell，將還原至 SQL Server 2005 或更新版本的內部部署實例的 **Adventureworks2016** 資料庫移轉至 Azure sql 受控執行個體。 您可以使用 Microsoft Azure PowerShell 中的模組，將資料庫從 SQL Server 實例遷移至 SQL 受控執行個體 `Az.DataMigration` 。

在本文中，您將學會如何：
> [!div class="checklist"]
>
> * 建立資源群組。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 在 Azure 資料庫移轉服務的實例中建立遷移專案。
> * 離線執行遷移。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文提供離線遷移的步驟，但您也可以在 [線上](howto-sql-server-to-azure-sql-managed-instance-powershell-online.md)遷移。


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


## <a name="sign-in-to-your-microsoft-azure-subscription"></a>登入您的 Microsoft Azure 訂用帳戶

使用 PowerShell 登入您的 Azure 訂用帳戶。 如需詳細資訊，請參閱 [使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps)文章。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用命令建立資源群組 [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) 。

下列範例會在 *美國東部* 區域建立名為 *myResourceGroup* 的資源群組。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>建立 Azure 資料庫移轉服務的執行個體

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>建立來源和目標連線的資料庫連接資訊物件

您可以使用 `New-AzDmsConnInfo` 需要下列參數的 Cmdlet 來建立資料庫連接資訊物件：

* *ServerType*。 要求的資料庫連接類型，例如 SQL、Oracle 或 MySQL。 使用 SQL Server 的 SQL 和 Azure SQL。
* *DataSource*。 SQL Server 實例或 Azure SQL Database 實例的名稱或 IP。
* *AuthType*。 連線的驗證類型，可以是 SqlAuthentication 或 WindowsAuthentication。
* *TrustServerCertificate*。 此參數會設定值，這個值會指出是否要在略過憑證鏈驗證信任時加密通道。 值可以是 `$true` 或 `$false` 。

下列範例會使用 SQL 驗證來建立來源 SQL Server 的連接資訊物件（稱為 *MySourceSQLServer* ）：

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

下一個範例會示範如何建立名為 ' targetmanagedinstance ' 的 Azure SQL 受控執行個體連接資訊：

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>提供移轉專案所用的資料庫

建立物件清單， `AzDataMigrationDatabaseInfo` 這些物件會將資料庫指定為 Azure 資料庫移轉服務專案的一部分，而這些物件可作為建立專案的參數提供。 您可以使用 Cmdlet `New-AzDataMigrationDatabaseInfo` 來建立 `AzDataMigrationDatabaseInfo` 。

下列範例 `AzDataMigrationDatabaseInfo` 會建立 **AdventureWorks2016** 資料庫的專案，並將其新增至清單中，以供建立專案的參數。

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>建立專案物件

最後，您 *可以使用來建立名為* *MyDMSProject* 的 Azure 資料庫移轉服務專案 `New-AzDataMigrationProject` ，並加入先前建立的來源與目標連接，以及要遷移的資料庫清單。

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>建立並啟動移轉工作

接著，建立並啟動 Azure 資料庫移轉服務工作。 這項工作需要來源和目標的連接認證資訊，以及要遷移的資料庫資料表清單，以及已提供的專案所提供的資訊做為必要條件。

### <a name="create-credential-parameters-for-source-and-target"></a>建立來源和目標的認證參數

建立連線安全性認證做為 [PSCredential](/dotnet/api/system.management.automation.pscredential) 物件。

下列範例會示範如何建立來源和目標連接的 *PSCredential* 物件，並將密碼變數 *$sourcePassword* 和 *$targetPassword* 提供給字串變數。

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>建立備份檔案共用物件

現在建立檔案共用物件，此物件代表 Azure 資料庫移轉服務可使用 Cmdlet 進行源資料庫備份的本機 SMB 網路共用 `New-AzDmsFileShare` 。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>建立選取的資料庫物件

下一步是使用 Cmdlet 來選取來源和目標資料庫 `New-AzDmsSelectedDB` 。

下列範例會將單一資料庫從 SQL Server 遷移至 Azure SQL 受控執行個體：

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

如果整個 SQL Server 實例需要隨即轉移至 Azure SQL 受控執行個體，則以下將提供從來源取得所有資料庫的迴圈。 在下列範例中，針對 $Server、$SourceUserName 和 $SourcePassword，請提供您的來源 SQL Server 詳細資料。

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure 儲存體容器的 SAS URI

建立包含 SAS URI 的變數，讓 Azure 資料庫管理服務能夠存取此服務將備份檔案上傳到的儲存體帳戶容器。

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Azure 資料庫移轉服務不支援使用帳戶層級 SAS 權杖。 您必須使用儲存體帳戶容器的 SAS URI。 [了解如何取得 Blob 容器的 SAS URI](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)。

### <a name="additional-configuration-requirements"></a>其他設定需求

另外還有一些您需要處理的額外需求：


* **選取**[登入]。 建立要遷移的登入清單，如下列範例所示：

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Azure 資料庫移轉服務目前僅支援遷移 SQL 登入。

* **選取 [代理程式作業**]。 建立要遷移的代理程式作業清單，如下列範例所示：

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Azure 資料庫移轉服務目前僅支援具有 T-sql 子系統作業步驟的作業。



### <a name="create-and-start-the-migration-task"></a>建立並啟動遷移工作

使用 `New-AzDataMigrationTask` Cmdlet 建立並啟動移轉工作。

#### <a name="specify-parameters"></a>指定參數

此 `New-AzDataMigrationTask` Cmdlet 預期會有下列參數：

* *TaskType*。 需要 SQL Server 至 Azure SQL 受控執行個體遷移類型 *MigrateSqlServerSqlDbMi* 所建立的遷移工作類型。 
* *資源組名*。 要在其中建立工作的資源群組名稱。
* *ServiceName*。 要在其中建立工作的 Azure 資料庫移轉服務執行個體。
* *專案名稱*。 要在其中建立工作的 Azure 資料庫移轉服務專案名稱。 
* *TaskName*。 要建立的工作名稱。 
* *SourceConnection*。 代表來源 SQL Server 連接的 AzDmsConnInfo 物件。
* *TargetConnection*。 代表目標 Azure SQL 受控執行個體連接的 AzDmsConnInfo 物件。
* SourceCred。 連線至來源伺服器所用的 [PSCredential](/dotnet/api/system.management.automation.pscredential) 物件。
* TargetCred。 連線至目標伺服器所用的 [PSCredential](/dotnet/api/system.management.automation.pscredential) 物件。
* SelectedDatabase。 代表來源和目標資料庫對應的 AzDataMigrationSelectedDB 物件。
* *BackupFileShare*。 此為 FileShare 物件，代表 Azure 資料庫移轉服務可移入來源資料庫備份的本機網路共用。
* *BackupBlobSasUri*。 此為 SAS URI，可讓 Azure 資料庫移轉服務存取此服務會將備份檔案上傳到的儲存體帳戶容器。 了解如何取得 Blob 容器的 SAS URI。
* *SelectedLogins*。 已選取要移轉的登入清單。
* *SelectedAgentJobs*。 已選取要移轉的代理程式作業清單。
* *SelectedLogins*。 已選取要移轉的登入清單。
* *SelectedAgentJobs*。 已選取要移轉的代理程式作業清單。



#### <a name="create-and-start-a-migration-task"></a>建立並啟動移轉工作

下列範例會建立並啟動名為 **myDMSTask** 的離線遷移工作：

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```


## <a name="monitor-the-migration"></a>監視移轉

若要監視遷移作業，請執行下列工作。

1. 將所有的遷移詳細資料合併到稱為 $CheckTask 的變數中。

    若要結合遷移詳細資料，例如與遷移相關聯的屬性、狀態和資料庫資訊，請使用下列程式碼片段：

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. 您 `$CheckTask` 可以使用變數來取得遷移工作的目前狀態。

    若要使用 `$CheckTask` 變數來取得遷移工作的目前狀態，您可以藉由查詢工作的 state 屬性來監視正在執行的遷移工作，如下列範例所示：

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```


## <a name="delete-the-instance-of-azure-database-migration-service"></a>刪除 Azure 資料庫移轉服務的實例

完成遷移之後，您可以刪除 Azure 資料庫移轉服務實例：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```


## <a name="next-steps"></a>後續步驟

在「 [什麼是 Azure 資料庫移轉服務](./dms-overview.md)」一文中，深入瞭解 Azure 資料庫移轉服務。

如需其他遷移案例 (來源/目標群組) 的詳細資訊，請參閱 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/)。