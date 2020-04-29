---
title: Powershell：將 SQL Server 遷移至 SQL Database
titleSuffix: Azure Database Migration Service
description: 瞭解如何透過 Azure 資料庫移轉服務使用 Azure PowerShell，從內部部署 SQL Server 遷移至 Azure SQL Database。
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
ms.openlocfilehash: f63f79402b457017257f1762c6ddc7e04c0ee1af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77650685"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>使用 Azure PowerShell 將 SQL Server 內部部署遷移至 Azure SQL Database

在本文中，您會使用 Microsoft Azure PowerShell，將已還原至內部部署 SQL Server 2016 (或更新版本) 執行個體的 **Adventureworks2012** 資料庫移轉至 Azure SQL Database。 您可以使用 Microsoft Azure PowerShell 中的 `Az.DataMigration` 模組，將資料庫從內部部署 SQL Server 執行個體移轉至 Azure SQL Database。

在本文中，您將學會如何：
> [!div class="checklist"]
>
> * 建立資源群組。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 在 Azure 資料庫移轉服務執行個體中建立移轉專案。
> * 執行移轉。

## <a name="prerequisites"></a>先決條件

若要完成這些步驟，您需要：

* [SQL Server 2016 或更新版本](https://www.microsoft.com/sql-server/sql-server-downloads) (任何版本)
* 啟用 SQL Server Express 安裝預設已停用的 TCP/IP 通訊協定。 依照[啟用或停用伺服器網路通訊協定](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文的說明，啟用 TCP/IP 通訊協定。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* Azure SQL Database 執行個體。 您可以依照[在 Azure 入口網站中建立 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) 一文中的詳細資料來建立 Azure SQL 資料庫執行個體。
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) \(英文\) 的 v3.3 或更新版本。
* 若要使用 Azure Resource Manager 部署模型來建立 Microsoft Azure 虛擬網路，可使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)為您的內部部署來源伺服器提供站對站連線能力給 Azure 資料庫移轉服務。
* 如[執行 SQL Server 遷移評估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)一文所述，使用 Data Migration Assistant 完成內部部署資料庫和架構遷移的評估
* 使用[install-Module PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)從 PowerShell 資源庫下載並安裝 Az microsoft.datamigration 模組;請務必使用 [以系統管理員身分執行] 來開啟 powershell 命令視窗。
* 確定用來連線至來源 SQL Server 執行個體的認證具有 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 權限。
* 確定用來連線至目標 Azure SQL DB 執行個體的認證，在目標 Azure SQL Database 資料庫上具有 CONTROL DATABASE 權限。
* Azure 訂用帳戶。 如果您沒有，請在開始前建立[免費](https://azure.microsoft.com/free/)帳戶。

## <a name="log-in-to-your-microsoft-azure-subscription"></a>登入您的 Microsoft Azure 訂用帳戶

根據[登入 Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)一文中的指示，使用 PowerShell 來登入您的 Azure 訂用帳戶。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 建立虛擬機器之前，先建立資源群組。

使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)命令來建立資源群組。

下列範例會在 EastUS** 地區建立名為 myResourceGroup** 的資源群組。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>建立 Azure 資料庫移轉服務的執行個體

您可以使用 `New-AzDataMigrationService` Cmdlet，來建立新的 Azure 資料庫移轉服務執行個體。 此 Cmdlet 預期有下列必要參數：

* *Azure 資源組名*。 如先前所示，您可以使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)命令來建立 Azure 資源群組，並提供其名稱作為參數。
* *服務名稱*。 該字串會對應至 Azure 資料庫移轉服務所需的唯一服務名稱 
* *位置*。 指定服務的位置。 指定 Azure 資料中心位置，例如美國西部或東南亞
* *Sku*。 此參數會對應至 DMS SKU 名稱。 目前支援的 SKU 名稱為 *GeneralPurpose_4vCores*。
* 虛擬子網路識別碼**。 您可以使用 Cmdlet [new-azvirtualnetworksubnetconfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig)來建立子網。 

下列範例會使用名為 *MyVNET* 的虛擬網路和名為 *MySubnet* 的子網路，在 *MyDMSResourceGroup* 資源群組中建立名為 *MyDMS* 的服務 (位於美國東部** 地區)。

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

您可以使用 `New-AzDmsConnInfo` Cmdlet 來建立資料庫連接資訊物件。 此 Cmdlet 預期有下列參數：

* *ServerType*。 要求的資料庫連接類型，例如 SQL、Oracle 或 MySQL。 使用 SQL Server 的 SQL 和 Azure SQL。
* *DataSource*。 SQL Server 執行個體或 Azure SQL 資料庫的名稱或 IP。
* *AuthType*。 連線的驗證類型，可以是 SqlAuthentication 或 WindowsAuthentication。
* TrustServerCertificate** 參數會設定一個值，指出通道是否會加密，同時繞過驗證信任的信任鏈結。 值可以是 true 或 false。

下列範例會使用 SQL 驗證，為名為 MySourceSQLServer 的來源 SQL Server 建立連線資訊物件：

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

下一個範例示範如何使用 SQL 驗證為名為 SQLAzureTarget 的 Azure SQL 資料庫伺服器建立連線資訊：

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>提供移轉專案所用的資料庫

建立 `AzDataMigrationDatabaseInfo` 物件清單，該清單會指定資料庫作為 Azure 資料庫移轉專案的一部份，並提供給建立物件所需的參數使用。 Cmdlet `New-AzDataMigrationDatabaseInfo`可以用來建立 AzDataMigrationDatabaseInfo。 

下列範例會為 **AdventureWorks2016** 資料庫建立 `AzDataMigrationDatabaseInfo` 專案，並將其新增至清單 (提供給建立物件所需的參數使用)。

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>建立專案物件

最後，您可以使用 `New-AzDataMigrationProject` 建立位在美國東部** 且名為 MyDMSProject** 的 Azure 資料庫移轉專案，並新增先前建立的來源和目標連線及資料庫清單來進行移轉。

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>建立並啟動移轉工作

最後，建立並啟動 Azure 資料庫移轉工作。 除了專案在作為必要元件建立時提供的資訊外，Azure 資料庫移轉工作還需要來源和目標的連線認證資訊，以及要移轉的資料庫清單資料表。

### <a name="create-credential-parameters-for-source-and-target"></a>建立來源和目標的認證參數

連線安全性認證可建立為 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) \(英文\) 物件。

下列範例示範如何將密碼提供為 $sourcePassword** 和 $targetPassword** 字串變數，以建立來源和目標的 PSCredential** 物件。

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>為移轉建立資料表對應並選取來源和目標參數

移轉所需的另一個參數是要移轉之來源和目標間的資料表對應。 建立資料表字典，為移轉提供來源和目標資料表之間的對應。 下列範例將針對 AdventureWorks 2016 資料庫人力資源結構描述說明來源和目標資料表之間的對應。

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

下一個步驟是使用 `New-AzDmsSelectedDB` Cmdlet 來選取來源和目標資料庫，並提供要移轉的資料表對應作為參數，如下列範例所示：

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>建立並啟動遷移工作

使用 `New-AzDataMigrationTask` Cmdlet 建立並啟動移轉工作。 此 Cmdlet 預期有下列參數：

* *TaskType*。 為 SQL Server 與 Azure SQL Database 移轉所建立的移轉工作類型，預期為 *MigrateSqlServerSqlDb*。 
* *資源組名*。 要在其中建立工作的資源群組名稱。
* *ServiceName*。 要在其中建立工作的 Azure 資料庫移轉服務執行個體。
* *專案名稱*。 要在其中建立工作的 Azure 資料庫移轉服務專案名稱。 
* *TaskName*。 要建立的工作名稱。 
* *SourceConnection*。 代表來源 SQL Server 連接的 AzDmsConnInfo 物件。
* *TargetConnection*。 代表目標 Azure SQL Database 連接的 AzDmsConnInfo 物件。
* SourceCred**。 連線至來源伺服器所用的 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 物件。
* TargetCred**。 連線至目標伺服器所用的 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 物件。
* SelectedDatabase**。 代表來源和目標資料庫對應的 AzDataMigrationSelectedDB 物件。
* *SchemaValidation*。 （選擇性，切換參數）在遷移之後，會在來源與目標之間執行架構資訊的比較。
* *DataIntegrityValidation*。 （選擇性，切換參數）在遷移之後，會在來源和目標之間執行總和檢查碼架構的資料完整性驗證。
* *QueryAnalysisValidation*。 （選擇性，切換參數）在遷移之後，會執行快速且智慧型的查詢分析，方法是從源資料庫中抓取查詢，並在目標中執行它們。

下列範例會建立並啟動名為 myDMSTask 的移轉工作：

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

下列範例會建立並啟動與上述相同的遷移工作，但也會執行這三項驗證：

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>監視移轉

您可以透過查詢工作的狀態屬性來監視移轉工作執行，如下列範例所示：

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>刪除 DMS 執行個體

完成移轉之後，您可以刪除 Azure DMS 執行個體：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>後續步驟

* 在 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\) 中檢閱移轉指引。
