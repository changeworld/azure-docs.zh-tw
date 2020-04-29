---
title: PowerShell：將 SQL Server 遷移至 SQL 受控實例
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 Azure PowerShell 和 Azure 資料庫移轉服務，從內部部署 SQL Server 遷移至 Azure SQL Database 受控實例。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77650719"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>使用 PowerShell & Azure 資料庫移轉服務，將 SQL Server 遷移至 SQL Database 受控實例

在本文中，您會使用 Microsoft Azure PowerShell，將還原到 SQL Server 2005 或更新版本之內部部署實例的**Adventureworks2016**資料庫移轉至 Azure SQL Database 受控實例。 您可以使用 Microsoft Azure PowerShell 中的`Az.DataMigration`模組，將資料庫從內部部署 SQL Server 實例遷移至 Azure SQL Database 受控實例。

在本文中，您將學會如何：
> [!div class="checklist"]
>
> * 建立資源群組。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 在 Azure 資料庫移轉服務的實例中建立遷移專案。
> * 執行移轉。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文包含如何執行線上和離線遷移的詳細資料。

## <a name="prerequisites"></a>先決條件

若要完成這些步驟，您需要：

* [SQL Server 2016 或](https://www.microsoft.com/sql-server/sql-server-downloads)更新版本（任何版本）。
* **AdventureWorks2016**資料庫的本機複本，可以從[這裡](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)下載。
* 啟用 SQL Server Express 安裝預設已停用的 TCP/IP 通訊協定。 依照[啟用或停用伺服器網路通訊協定](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文的說明，啟用 TCP/IP 通訊協定。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* Azure 訂用帳戶。 如果您沒有，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
* Azure SQL Database 受控實例。 您可以遵循[建立 Azure SQL Database 受控實例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)一文中的詳細資料，來建立 Azure SQL Database 受控實例。
* 下載並安裝[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3 版或更新版本。
* 使用 Azure Resource Manager 部署模型建立的 Microsoft Azure 虛擬網路，它會使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)為您的內部部署來源伺服器提供站對站連線能力的 Azure 資料庫移轉服務。
* 如[執行 SQL Server 遷移評估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)一文所述，使用 Data Migration Assistant 完成內部部署資料庫和架構遷移的評估。
* 使用`Az.DataMigration` [Install-module PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)從 PowerShell 資源庫下載並安裝模組（0.7.2 版版或更新版本）。
* 若要確保用來連接至來源 SQL Server 實例的認證具有[CONTROL Server](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)許可權。
* 若要確保用來連接目標 Azure SQL Database 受控實例的認證，具有目標 Azure SQL Database 受控實例資料庫的 CONTROL DATABASE 許可權。

    > [!IMPORTANT]
    > 針對線上遷移，您必須已設定您的 Azure Active Directory 認證。 如需詳細資訊，請參閱[使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體一](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)文。

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>登入您的 Microsoft Azure 訂用帳戶

使用 PowerShell 登入您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[使用 Azure PowerShell 登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)一文。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用[`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)命令建立資源群組。

下列範例會在*美國東部*區域建立名為*myResourceGroup*的資源群組。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>建立 Azure 資料庫移轉服務的執行個體

您可以使用 `New-AzDataMigrationService` Cmdlet，來建立新的 Azure 資料庫移轉服務執行個體。
此 Cmdlet 預期有下列必要參數：

* *Azure 資源組名*。 如先前所[`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)示，您可以使用命令來建立 Azure 資源群組，並提供其名稱作為參數。
* *服務名稱*。 對應至 Azure 資料庫移轉服務所需唯一服務名稱的字串。
* *位置*。 指定服務的位置。 指定 Azure 資料中心位置，例如 [美國西部] 或 [東南亞]。
* *Sku*。 此參數會對應至 DMS SKU 名稱。 目前支援的 Sku 名稱為*Basic_1vCore*、 *Basic_2vCores* *GeneralPurpose_4vCores*。
* 虛擬子網路識別碼**。 您可以使用 Cmdlet [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig)來建立子網。

下列範例會使用名為*MyVNET*的虛擬網路和名為*MySubnet*的子網，在位於*美國東部*區域的資源群組*MyDMSResourceGroup*中建立名為*MyDMS*的服務。

> [!IMPORTANT]
> 下面的程式碼片段適用于離線遷移，這不需要以 Premium SKU 為基礎的 Azure 資料庫移轉服務實例。 針對線上遷移，-Sku 參數的值必須包含 Premium SKU。

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

您可以使用`New-AzDmsConnInfo` Cmdlet 來建立資料庫連接資訊物件，這需要下列參數：

* *ServerType*。 要求的資料庫連接類型，例如 SQL、Oracle 或 MySQL。 使用 SQL Server 的 SQL 和 Azure SQL。
* *DataSource*。 SQL Server 實例或 Azure SQL Database 實例的名稱或 IP。
* *AuthType*。 連線的驗證類型，可以是 SqlAuthentication 或 WindowsAuthentication。
* *TrustServerCertificate*。 這個參數會設定一個值，指出通道是否會加密，同時略過驗證信任的憑證鏈。 此值可以是`$true`或`$false`。

下列範例會使用 SQL 驗證來建立名為*MySourceSQLServer*之來源 SQL Server 的連接資訊物件：

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

下一個範例會示範如何使用 SQL 驗證來建立名為 ' targetmanagedinstance.database.windows.net ' 的 Azure SQL Database 受控實例伺服器的連線資訊：

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>提供移轉專案所用的資料庫

建立`AzDataMigrationDatabaseInfo`物件清單，以指定資料庫做為 Azure 資料庫移轉服務專案的一部分，可提供做為建立專案的參數。 您可以使用 Cmdlet `New-AzDataMigrationDatabaseInfo`來建立。 `AzDataMigrationDatabaseInfo`

下列範例會建立`AzDataMigrationDatabaseInfo` **AdventureWorks2016**資料庫的專案，並將其新增至清單，以作為專案建立的參數。

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>建立專案物件

最後，您可以使用`New-AzDataMigrationProject`在*美國東部*建立名為*MyDMSProject*的 Azure 資料庫移轉服務專案，並新增先前建立的來源和目標連接，以及要遷移的資料庫清單。

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

接下來，建立並啟動 Azure 資料庫移轉服務工作。 這項工作需要來源和目標的連線認證資訊，以及要遷移的資料庫資料表清單，以及已在建立為必要條件的專案中提供的資訊。

### <a name="create-credential-parameters-for-source-and-target"></a>建立來源和目標的認證參數

以[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)物件的形式建立連接安全性認證。

下列範例示範如何建立來源和目標連接的*PSCredential*物件，並提供密碼做為字串變數 *$sourcePassword*和 *$targetPassword*。

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>建立備份檔案共用物件

現在，建立一個檔案共用物件，代表 Azure 資料庫移轉服務可使用`New-AzDmsFileShare` Cmdlet 來進行源資料庫備份的本機 SMB 網路共用。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>建立選取的資料庫物件

下一個步驟是使用`New-AzDmsSelectedDB` Cmdlet 來選取來源和目標資料庫。

下列範例是用來將單一資料庫從 SQL Server 遷移至 Azure SQL Database 受控實例：

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

如果整個 SQL Server 實例需要隨即轉移到 Azure SQL Database 受控實例中，則會在下方提供從來源執行所有資料庫的迴圈。 在下列範例中，針對 $Server、$SourceUserName 和 $SourcePassword，請提供您的來源 SQL Server 詳細資料。

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

### <a name="additional-configuration-requirements"></a>其他設定需求

您還需要處理一些額外的需求，但它們會因您執行的是離線或線上遷移而有所不同。

#### <a name="offline-migrations"></a>離線遷移

僅適用于離線遷移，請執行下列其他設定工作。

* **選取**[登入]。 建立要遷移的登入清單，如下列範例所示：

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 資料庫移轉服務僅支援遷移 SQL 登入。

* **選取 [代理程式作業**]。 建立要遷移的 agent 作業清單，如下列範例所示：

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 資料庫移轉服務僅支援具有 T-sql 子系統作業步驟的作業。

#### <a name="online-migrations"></a>線上遷移

僅適用于線上遷移，請執行下列其他設定工作。

* **設定 Azure Active Directory 應用程式**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **設定儲存體資源**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>建立並開始進行遷移工作

使用 `New-AzDataMigrationTask` Cmdlet 建立並啟動移轉工作。

#### <a name="specify-parameters"></a>指定參數

##### <a name="common-parameters"></a>一般參數

無論您是執行離線或線上遷移，此`New-AzDataMigrationTask` Cmdlet 都需要下列參數：

* *TaskType*。 要為從 SQL Server 到 Azure SQL Database 受控執行個體的移轉建立的移轉工作類型，預期為 MigrateSqlServerSqlDbMi**。 
* *資源組名*。 要在其中建立工作的資源群組名稱。
* *ServiceName*。 要在其中建立工作的 Azure 資料庫移轉服務執行個體。
* *專案名稱*。 要在其中建立工作的 Azure 資料庫移轉服務專案名稱。 
* *TaskName*。 要建立的工作名稱。 
* *SourceConnection*。 代表來源 SQL Server 連接的 AzDmsConnInfo 物件。
* *TargetConnection*。 代表目標 Azure SQL Database 受控執行個體連接的 AzDmsConnInfo 物件。
* SourceCred**。 連線至來源伺服器所用的 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 物件。
* TargetCred**。 連線至目標伺服器所用的 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 物件。
* SelectedDatabase**。 代表來源和目標資料庫對應的 AzDataMigrationSelectedDB 物件。
* *BackupFileShare*。 此為 FileShare 物件，代表 Azure 資料庫移轉服務可移入來源資料庫備份的本機網路共用。
* *BackupBlobSasUri*。 此為 SAS URI，可讓 Azure 資料庫移轉服務存取此服務會將備份檔案上傳到的儲存體帳戶容器。 了解如何取得 Blob 容器的 SAS URI。
* *SelectedLogins*。 已選取要移轉的登入清單。
* *SelectedAgentJobs*。 已選取要移轉的代理程式作業清單。

##### <a name="additional-parameters"></a>其他參數

此`New-AzDataMigrationTask` Cmdlet 也需要您所執行之遷移、離線或線上類型特有的參數。

* **離線遷移**。 針對離線遷移，此`New-AzDataMigrationTask` Cmdlet 也需要下列參數：

  * *SelectedLogins*。 已選取要移轉的登入清單。
  * *SelectedAgentJobs*。 已選取要移轉的代理程式作業清單。

* **線上遷移**。 針對線上遷移，此`New-AzDataMigrationTask` Cmdlet 也需要下列參數。

* *AzureActiveDirectoryApp*。 Active Directory 應用程式。
* *StorageResourceID*。 儲存體帳戶的資源識別碼。

#### <a name="create-and-start-an-offline-migration-task"></a>建立和啟動離線遷移工作

下列範例會建立並啟動名為**myDMSTask**的離線遷移工作：

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

#### <a name="create-and-start-an-online-migration-task"></a>建立並啟動線上遷移工作

下列範例會建立並啟動名為**myDMSTask**的線上遷移工作：

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
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
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>監視移轉

若要監視遷移作業，請執行下列工作。

1. 將所有的遷移詳細資料合併到稱為 $CheckTask 的變數。

    若要結合遷移詳細資料（例如屬性、狀態和與遷移相關聯的資料庫資訊），請使用下列程式碼片段：

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. 使用`$CheckTask`變數來取得遷移工作的目前狀態。

    若要使用`$CheckTask`變數來取得遷移工作的目前狀態，您可以藉由查詢工作的狀態屬性來監視執行中的遷移工作，如下列範例所示：

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

## <a name="performing-the-cutover-online-migrations-only"></a>執行轉換（僅限線上遷移）

透過線上遷移，會執行資料庫的完整備份和還原，然後繼續進行還原儲存在 BackupFileShare 中的交易記錄。

當 Azure SQL Database 受控實例中的資料庫以最新的資料更新，而且與源資料庫同步時，您可以執行轉換。

下列範例會完成 cutover\migration。 使用者會自行叫用此命令。

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>刪除 Azure 資料庫移轉服務的實例

完成遷移之後，您可以刪除 Azure 資料庫移轉服務實例：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>其他資源

如需其他遷移案例（來源/目標群組）的相關資訊，請參閱《 Microsoft[資料庫移轉指南》](https://datamigration.microsoft.com/)。

## <a name="next-steps"></a>後續步驟

如需瞭解 Azure 資料庫移轉服務的詳細資訊，請參閱[什麼是 Azure 資料庫移轉服務？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
