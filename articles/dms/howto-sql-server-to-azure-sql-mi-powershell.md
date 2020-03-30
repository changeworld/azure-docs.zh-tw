---
title: PowerShell：將 SQL 伺服器遷移到 SQL 託管實例
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 Azure PowerShell 和 Azure 資料庫移轉服務從本地 SQL Server 遷移到 Azure SQL 資料庫託管實例。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650719"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>使用 PowerShell & Azure 資料庫移轉服務將 SQL 伺服器遷移到 SQL 資料庫託管實例

在本文中，您可以使用 Microsoft Azure PowerShell 將還原到 SQL Server 2005 或以上本地實例的**Adventureworks2016**資料庫移轉到 Azure SQL 資料庫託管實例。 通過使用 Microsoft Azure PowerShell 中的模組，`Az.DataMigration`可以將資料庫從本地 SQL Server 實例遷移到 Azure SQL 資料庫託管實例。

在本文中，您將學會如何：
> [!div class="checklist"]
>
> * 建立資源群組。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 在 Azure 資料庫移轉服務實例中創建遷移專案。
> * 執行移轉。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文包括有關如何執行連線和離線遷移的詳細資訊。

## <a name="prerequisites"></a>Prerequisites

若要完成這些步驟，您需要：

* [SQL Server 2016 或以上](https://www.microsoft.com/sql-server/sql-server-downloads)（任何版本）。
* **AdventureWorks2016**資料庫的本機複本，可[在此處](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)下載。
* 啟用 SQL Server Express 安裝預設已停用的 TCP/IP 通訊協定。 依照[啟用或停用伺服器網路通訊協定](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文的說明，啟用 TCP/IP 通訊協定。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* Azure 訂用帳戶。 如果沒有，請先[創建一個免費帳戶](https://azure.microsoft.com/free/)。"
* Azure SQL 資料庫託管實例。 可以通過按照文章["創建 Azure SQL 資料庫託管實例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)"中的詳細資訊創建 Azure SQL 資料庫託管實例。
* 下載並安裝[資料移轉助手](https://www.microsoft.com/download/details.aspx?id=53595)v3.3 或更高版本。
* 使用 Azure 資源管理器部署模型創建的 Microsoft Azure 虛擬網路，該模型通過使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)向 Azure 資料庫移轉服務提供與本地源伺服器的網站到網站的連接。
* 使用資料移轉助手完成對本機資料庫和架構遷移的評估，如[執行 SQL Server 遷移評估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)一文中所述。
* 使用`Az.DataMigration`[安裝模組 PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)從 PowerShell 庫下載並安裝模組（版本 0.7.2 或更高版本）。
* 確保用於連接到源 SQL Server 實例的憑據具有[控制伺服器](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)許可權。
* 確保用於連接到目標 Azure SQL 資料庫託管實例的憑據具有目標 Azure SQL 資料庫託管實例資料庫上的 CONTROL DATABASE 許可權。

    > [!IMPORTANT]
    > 對於連線遷移，必須已設置 Azure 活動目錄憑據。 有關詳細資訊，請參閱[文章"使用門戶創建可以訪問資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)"。

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>登錄到 Microsoft Azure 訂閱

使用 PowerShell 登錄到 Azure 訂閱。 有關詳細資訊，請參閱使用 Azure [PowerShell 登錄](https://docs.microsoft.com/powershell/azure/authenticate-azureps)的文章。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用[`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)命令創建資源組。

下面的示例*在美國東部*區域創建名為*myResourceGroup*的資源組。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>建立 Azure 資料庫移轉服務的執行個體

您可以使用 `New-AzDataMigrationService` Cmdlet，來建立新的 Azure 資料庫移轉服務執行個體。
此 Cmdlet 預期有下列必要參數：

* *Azure 資源組名稱*。 可以使用[`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)命令創建 Azure 資源組（如前面所示）並將其名稱作為參數提供。
* *服務名稱*. 對應于 Azure 資料庫移轉服務所需的唯一服務名稱的字串。
* *位置*。 指定服務的位置。 指定 Azure 資料中心位置，如美國西部或東南亞。
* *斯庫*. 此參數會對應至 DMS SKU 名稱。 目前支援的Sku名稱是*Basic_1vCore**Basic_1vCore，Basic_2vCores，GeneralPurpose_4vCores。* *GeneralPurpose_4vCores*
* 虛擬子網路識別碼**。 您可以使用 Cmdlet[`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig)創建子網。

下面的示例使用名為*MyVNET*的虛擬網路和名為*MySubnet*的子網，*在位於美國東部*區域的資源組*MyDMS 資源組*中創建名為*MyDMS*的服務。

> [!IMPORTANT]
> 下面的程式碼片段用於離線遷移，它不需要基於高級 SKU 的 Azure 資料庫移轉服務的實例。 對於連線遷移，-Sku 參數的值必須包括高級 SKU。

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

您可以使用`New-AzDmsConnInfo`Cmdlet 創建資料庫連接資訊物件，Cmdlet 需要以下參數：

* *伺服器類型*。 要求的資料庫連接類型，例如 SQL、Oracle 或 MySQL。 使用 SQL Server 的 SQL 和 Azure SQL。
* *資料來源*. SQL Server 實例或 Azure SQL 資料庫實例的名稱或 IP。
* *奧思類型*. 連線的驗證類型，可以是 SqlAuthentication 或 WindowsAuthentication。
* *信任伺服器憑證*。 此參數設置一個值，指示通道是否加密，同時繞過憑證連結來驗證信任。 該值可以是`$true`或`$false`。

以下示例使用 sql 身份驗證為名為*MySourceSQLServer*的源 SQL Server 創建連接資訊物件：

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

下一個示例演示如何使用 sql 身份驗證為名為"targetmanagedinstance.database.windows.net"的 Azure SQL 資料庫託管實例伺服器創建連接資訊：

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>提供移轉專案所用的資料庫

創建指定資料庫作為`AzDataMigrationDatabaseInfo`Azure 資料庫移轉服務專案的一部分的物件清單，該清單可以作為創建專案的參數提供。 您可以使用 Cmdlet`New-AzDataMigrationDatabaseInfo`創建`AzDataMigrationDatabaseInfo`。

下面的示例為`AzDataMigrationDatabaseInfo`**AdventureWorks2016**資料庫創建專案，並將其添加到要作為專案創建參數提供的清單中。

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>建立專案物件

最後，您可以使用之前`New-AzDataMigrationProject`創建的源和目標連接以及要遷移的資料庫清單，創建*位於美國東部的*名為*MyDMSProject*的 Azure 資料庫移轉服務專案。

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

接下來，創建並啟動 Azure 資料庫移轉服務任務。 此任務需要源和目標的連接憑據資訊，以及要遷移的資料庫表的清單以及作為先決條件創建的專案已提供的資訊的清單。

### <a name="create-credential-parameters-for-source-and-target"></a>建立來源和目標的認證參數

將連接安全憑據創建為[PS憑據](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)物件。

下面的示例演示了為源和目標連接創建*PSCredential*物件，將密碼作為字串變數 *$sourcePassword*和 *$targetPassword。*

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>創建備份檔案共用物件

現在創建一個檔共用物件，表示 Azure 資料庫移轉服務可以使用`New-AzDmsFileShare`Cmdlet 獲取源資料庫備份的本地 SMB 網路共用。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>建立選取的資料庫物件

下一步是使用`New-AzDmsSelectedDB`Cmdlet 選擇源資料庫和目標資料庫。

以下示例用於將單個資料庫從 SQL Server 遷移到 Azure SQL 資料庫託管實例：

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

如果整個 SQL Server 實例需要提升和移入 Azure SQL 資料庫託管實例，則下面將提供一個迴圈，用於從源獲取所有資料庫。 在下面的示例中，對於$Server、$SourceUserName和$SourcePassword，請提供源 SQL Server 詳細資訊。

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

### <a name="additional-configuration-requirements"></a>其他配置要求

您需要解決一些附加要求，但它們因執行離線還是連線遷移而異。

#### <a name="offline-migrations"></a>離線遷移

僅對於離線遷移，執行以下其他配置任務。

* **選擇登錄名**。 創建要遷移的登錄清單，如以下示例所示：

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 資料庫移轉服務僅支援遷移 SQL 登錄。

* **選擇代理作業**。 創建要遷移的代理作業清單，如以下示例所示：

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 資料庫移轉服務僅支援具有 T-SQL 子系統作業步驟的作業。

#### <a name="online-migrations"></a>線上遷移

僅對於連線遷移，執行以下其他配置任務。

* **配置 Azure 活動目錄應用**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **配置存儲資源**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>創建並啟動遷移任務

使用 `New-AzDataMigrationTask` Cmdlet 建立並啟動移轉工作。

#### <a name="specify-parameters"></a>指定參數

##### <a name="common-parameters"></a>一般參數

無論您是執行離線還是連線遷移，Cmdlet`New-AzDataMigrationTask`都希望使用以下參數：

* *任務類型*。 要為從 SQL Server 到 Azure SQL Database 受控執行個體的移轉建立的移轉工作類型，預期為 MigrateSqlServerSqlDbMi**。 
* *資源組名稱*. 要在其中建立工作的資源群組名稱。
* *服務名稱*。 要在其中建立工作的 Azure 資料庫移轉服務執行個體。
* *專案名稱*。 要在其中建立工作的 Azure 資料庫移轉服務專案名稱。 
* *任務名稱*。 要建立的工作名稱。 
* *SourceConnection*。 表示源 SQL 伺服器連接的 AzDmsConnInfo 物件。
* *TargetConnection*。 表示目標 Azure SQL 資料庫託管實例連接的 AzDmsConnInfo 物件。
* SourceCred**。 連線至來源伺服器所用的 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 物件。
* TargetCred**。 連線至目標伺服器所用的 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 物件。
* SelectedDatabase**。 表示源資料庫和目標資料庫映射的 AzData 遷移選擇DB 物件。
* *BackupFileShare*。 此為 FileShare 物件，代表 Azure 資料庫移轉服務可移入來源資料庫備份的本機網路共用。
* *BackupBlobSasUri*。 此為 SAS URI，可讓 Azure 資料庫移轉服務存取此服務會將備份檔案上傳到的儲存體帳戶容器。 了解如何取得 Blob 容器的 SAS URI。
* *SelectedLogins*。 已選取要移轉的登入清單。
* *SelectedAgentJobs*。 已選取要移轉的代理程式作業清單。

##### <a name="additional-parameters"></a>其他參數

`New-AzDataMigrationTask` Cmdlet 還期望執行的遷移類型（離線或連線）是唯一的參數。

* **離線遷移**。 對於離線遷移，Cmdlet`New-AzDataMigrationTask`還需要以下參數：

  * *SelectedLogins*。 已選取要移轉的登入清單。
  * *SelectedAgentJobs*。 已選取要移轉的代理程式作業清單。

* **線上遷移**。 對於連線遷移，Cmdlet`New-AzDataMigrationTask`還需要以下參數。

* *Azure ActiveDirectoryApp*. 活動目錄應用程式。
* *存儲資源識別碼*。 存儲帳戶的資源識別碼。

#### <a name="create-and-start-an-offline-migration-task"></a>創建並啟動離線遷移任務

以下示例創建並啟動名為**myDMSTask**的離線遷移任務 ：

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

#### <a name="create-and-start-an-online-migration-task"></a>創建並啟動連線遷移任務

以下示例創建並啟動名為**myDMSTask**的線上遷移任務 ：

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

要監視遷移，可以執行以下任務。

1. 將所有遷移詳細資訊合併到稱為$CheckTask變數中。

    要合併與遷移關聯的遷移詳細資訊（如屬性、狀態和資料庫資訊），請使用以下程式碼片段：

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. 使用`$CheckTask`變數獲取遷移任務的目前狀態。

    要使用`$CheckTask`變數獲取遷移任務的目前狀態，可以通過查詢任務的狀態屬性來監視正在運行的遷移任務，如以下示例所示：

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

## <a name="performing-the-cutover-online-migrations-only"></a>執行截止（僅限連線遷移）

通過連線遷移，將執行資料庫的完整備份和還原，然後繼續還原存儲在 BackupFileShare 中的事務日誌。

當 Azure SQL 資料庫託管實例中的資料庫使用最新資料更新並與源資料庫同步時，可以執行複製。

下面的示例將完成截止遷移。 使用者自行調用此命令。

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>刪除 Azure 資料庫移轉服務的實例

遷移完成後，可以刪除 Azure 資料庫移轉服務實例：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>其他資源

有關其他遷移方案（源/目標對）的資訊，請參閱 Microsoft[資料庫移轉指南](https://datamigration.microsoft.com/)。

## <a name="next-steps"></a>後續步驟

在文章"[什麼是 Azure 資料庫移轉服務？？](https://docs.microsoft.com/azure/dms/dms-overview)
