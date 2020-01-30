---
title: 設定兩個受控執行個體與 SQL Server 之間的異動複寫
description: 在發行者受控執行個體、散發者受控執行個體、Azure VM 上的 SQL Server 訂閱者，以及必要的網路元件 (例如私人 DNS 區域和 VPN 對等互連) 之間設定複寫的教學課程。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: fa6e393500e9deeb91ee84aa5255320003817f08
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719886"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>教學課程：設定兩個受控執行個體與 SQL Server 之間的異動複寫


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> - 將受控執行個體設定為複寫發行者。 
> - 將受控執行個體設定為複寫散發者。 
> - 將 SQL Server 設定為訂閱者。 

![SQL MI 發行者、SQL MI 散發者與 SQL Server 訂閱者之間的複寫](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

本教學課程適用於有經驗的對象，並假設使用者熟知如何部署和連線至受控執行個體和 Azure 中的 SQL Server VM。 因此，本教學課程中的特定步驟只會略為帶過。 

若要深入了解，請參閱 [Azure SQL Database 受控執行個體概觀](sql-database-managed-instance-index.yml)、[功能](sql-database-managed-instance.md)和 [SQL 異動複寫](sql-database-managed-instance-transactional-replication.md)等文章。

若要設定受控執行個體發行者與受控執行個體訂閱者之間的複寫，請參閱[設定兩個受控執行個體之間的異動複寫](replication-with-sql-database-managed-instance.md)。 

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，請確定您具有下列必要條件：

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。 
- 在相同虛擬網路中部署兩個受控執行個體的經驗。 
- 一個 SQL Server 訂閱者 (可以是內部部署或 Azure VM)。 本教學課程使用 Azure VM。  
- [SQL Server Management Studio (SSMS) 18.0 或更新版本](/sql/ssms/download-sql-server-management-studio-ssms)。
- 最新版的 [Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0)。
- 連接埠 445 和 1433 允許 Azure 防火牆和 Windows 防火牆上的 SQL 流量。 

## <a name="1---create-the-resource-group"></a>1 - 建立資源群組
使用下列 PowerShell 程式碼片段建立新的資源群組︰

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 - 建立兩個受控執行個體
使用 [Azure 入口網站](https://portal.azure.com)，在這個新的資源群組內建立兩個受控執行個體。 

- 發行者受控執行個體的名稱應為：`sql-mi-publisher` (以及幾個隨機的字元)，虛擬網路的名稱應為 `vnet-sql-mi-publisher`。
- 散發者受控執行個體的名稱應為：`sql-mi-distributor` (以及幾個隨機的字元)，且應_位於與發行者受控執行個體相同的虛擬網路中_。

   ![將發行者 VNet 用於散發者](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

如需建立受控執行個體的詳細資訊，請參閱[在入口網站中建立受控執行個體](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > 為了簡單起見，且因為這是最常見的組態，本教學課程建議將散發者受控執行個體放在與發行者相同的虛擬網路中。 不過，您可以在不同的虛擬網路中建立散發者。 若要這麼做，您必須在發行者與散發者的虛擬網路之間設定 VPN 對等互連，然後在散發者與訂閱者的虛擬網路之間設定 VPN 對等互連。 

## <a name="3---create-a-sql-server-vm"></a>3 - 建立 SQL Server VM
使用 [Azure 入口網站](https://portal.azure.com)建立 SQL Server 虛擬機器。 SQL Server 虛擬機器應具有下列特性：

- 名稱：`sql-vm-sub`
- 映像：SQL Server 2016 或更新版本
- 資源群組：與受控執行個體相同
- 虛擬網路：`sql-vm-sub-vnet` 

如需將 SQL Server VM 部署至 Azure 的詳細資訊，請參閱[快速入門：建立 SQL Server VM](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)。

## <a name="4---configure-vpn-peering"></a>4 - 設定 VPN 對等互連
設定 VPN 對等互連，以在兩個受控執行個體的虛擬網路與 SQL Server 的虛擬網路之間啟用通訊。 若要這麼做，請使用下列 PowerShell 程式碼片段：

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet 

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

建立 VPN 對等互連後，請在您的 SQL Server 上啟動 SQL Server Management Studio (SSMS)，並連線至這兩個受控執行個體，以測試連線能力。 如需使用 SSMS 連線至受控執行個體的詳細資訊，請參閱[使用 SSMS 連線至 MI](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance)。 

![測試對受控執行個體的連線能力](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 - 建立私人 DNS 區域

私人 DNS 區域可讓受控實例與 SQL Server 之間進行 DNS 路由。 

### <a name="create-private-dns-zone"></a>建立私人 DNS 區域
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [建立資源]  以建立 Azure 資源。 
1. 在 Azure Marketplace 上搜尋 `private dns zone`。 
1. 選擇 Microsoft 所發佈的 [私人 DNS 區域]  資源，然後選取 [建立]  以建立 DNS 區域。 
1. 從下拉式清單中選擇訂閱和資源群組。 
1. 為 DNS 區域提供任意名稱，例如 `repldns.com`。 

   ![建立私人 DNS 區域](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. 選取 [檢閱 + 建立]  。 檢查私人 DNS 區域的參數，然後選取 [建立]  以建立您的資源。 

### <a name="create-a-record"></a>建立記錄

1. 移至新的**私人 DNS 區域**，然後選取 [概觀]  。 
1. 選取 [+ 記錄集]  以建立新的 A 記錄。 
1. 提供 SQL Server VM 的名稱，以及私人內部 IP 位址。 

   ![設定 A 記錄](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. 選取 [確定]  以建立 A 記錄。 

### <a name="link-the-virtual-network"></a>連結虛擬網路

1. 移至新的**私人 DNS 區域**，然後選取 [虛擬網路連結]  。 
1. 選取 [+ 新增]  。 
1. 提供連結的名稱，例如 `Pub-link`。 
1. 從下拉式選單中選取您的訂閱，然後選取發行者受控執行個體的虛擬網路。 
1. 核取 [啟用自動註冊]  旁的方塊。 

   ![建立 VNet 連結](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. 選取 [確定]  以連結您的虛擬網路。 
1. 重複上述步驟，為訂閱者虛擬網路新增連結 (具有 `Sub-link` 之類的名稱)。 


## <a name="6---create-azure-storage-account"></a>6 - 建立 Azure 儲存體帳戶

為工作目錄[建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)，然後在儲存體帳戶內建立[檔案共用](../storage/files/storage-how-to-create-file-share.md)。 

複製下列格式的檔案共用路徑：`\\storage-account-name.file.core.windows.net\file-share-name`   

範例： `\\replstorage.file.core.windows.net\replshare`

複製下列格式的儲存體存取金鑰連接字串：`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

範例： `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)。 


## <a name="7---create-a-database"></a>7 - 建立資料庫
在發行者 MI 上建立新的資料庫。 若要這樣做，請依照下列步驟執行：

1. 在您的 SQL Server 上啟動 SQL Server Management Studio (SSMS)。 
1. 連線至 `sql-mi-publisher` 受控執行個體。 
1. 開啟 [新增查詢]  視窗，然後執行下列 T-SQL 查詢以建立資料庫：

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 - 設定散發 
建立連線並擁有範例資料庫之後，您就可以在 `sql-mi-distributor` 受控執行個體上設定散發。 若要這樣做，請依照下列步驟執行：

1. 在您的 SQL Server 上啟動 SQL Server Management Studio (SSMS)。 
1. 連線至 `sql-mi-distributor` 受控執行個體。 
1. 開啟 [新增查詢]  視窗，然後執行下列 Transact-SQL 程式碼，以在散發者受控執行個體上設定散發： 

   ```sql
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > 對於 @working_directory 參數請務必使用反斜線 (`\`)。 若使用正斜線 (`/`)，在連線至檔案共用時可能會導致錯誤。 

1. 連線至 `sql-mi-publisher` 受控執行個體。 
1. 開啟 [新增查詢]  視窗，然後執行下列 Transact-SQL 程式碼，以在發行者上註冊散發者： 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 - 建立發行集
設定散發後，您現在就可以建立發行集。 若要這樣做，請依照下列步驟執行： 

1. 在您的 SQL Server 上啟動 SQL Server Management Studio (SSMS)。 
1. 連線至 `sql-mi-publisher` 受控執行個體。 
1. 在 [物件總管]  中展開 [複寫]  節點，然後以滑鼠右鍵按一下 [本機發行集]  資料夾。 選取 [新增發行集...]  。 
1. 選取 [下一步]  ，移出歡迎頁面。 
1. 在 [發行集資料庫]  頁面上，選取您先前建立的 `ReplTutorial` 資料庫。 選取 [下一步]  。 
1. 在 [發行集類型]  頁面中，選取 [交易式發行集]  。 選取 [下一步]  。 
1. 在 [發行項]  頁面上，核取 [資料表]  旁的方塊。 選取 [下一步]  。 
1. 在 [篩選資料表的資料列]  頁面上選取 [下一步]  ，而不新增任何篩選。 
1. 在 [快照集代理程式]  頁面上，核取 [立即建立快照集，並保留快照集為可使用狀態，以初始化訂閱]  旁的方塊。 選取 [下一步]  。 
1. 在 [代理程式安全性]  頁面上，選取 [安全性設定...]  。提供要用於快照集代理程式以及連線至發行者的 SQL Server 登入認證。 選取 [確定]  ，以關閉 [快照集代理程式安全性]  頁面。 選取 [下一步]  。 

   ![設定快照集代理程式安全性](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. 在 [精靈動作]  頁面上，選擇 [建立發行集]  ，並 (選擇性地) 選擇 [產生具建立發行集步驟的指令碼檔案]  (如果您想要儲存此指令碼以供後續使用的話)。 
1. 在 [完成精靈]  頁面上，將您的發行集命名為 `ReplTest`，然後選取 [下一步]  以建立發行集。 
1. 發行集建立後，請重新整理 [物件總管]  中的 [複寫]  節點，然後展開 [本機發行集]  ，以查看新的發行集。 


## <a name="10---create-the-subscription"></a>10 - 建立訂閱 

發行集建立後，您可以建立訂閱。 若要這樣做，請依照下列步驟執行： 

1. 在您的 SQL Server 上啟動 SQL Server Management Studio (SSMS)。 
1. 連線至 `sql-mi-publisher` 受控執行個體。 
1. 開啟 [新增查詢]  視窗，然後執行下列 Transact-SQL 程式碼，以新增訂閱和散發代理程式。 以 DNS 做為訂閱者名稱的一部分。 

```sql
use [ReplTutorial]
exec sp_addsubscription 
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub', 
@subscription_type = N'Push', 
@sync_type = N'automatic', 
@article = N'all', 
@update_mode = N'read only', 
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub', 
@job_login = N'azureuser', 
@job_password = '<Complex Password>', 
@subscriber_security_mode = 0, 
@subscriber_login = N'azureuser', 
@subscriber_password = '<Complex Password>', 
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>11 - 測試複寫 

設定複寫後，您可以在發行者上插入新項目，並監看這些變更傳播到訂閱者，藉以測試複寫。 

執行下列 T-SQL 程式碼片段，以檢視訂閱者上的資料列：

```sql
Use ReplSub
select * from dbo.ReplTest
```

執行下列 T-SQL 程式碼片段，在發行者上插入其他資料列，然後再次查看訂閱者上的資料列。 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>清除資源

1. 瀏覽至您在 [Azure 入口網站](https://portal.azure.com)中的資源群組。 
1. 選取受控執行個體，然後選取 [刪除]  。 在文字方塊中輸入 `yes` 以確認您要刪除資源，然後選取 [刪除]  。 此程序可能需要一些時間才能在背景中完成，在其完成前，您將無法刪除*虛擬叢集*或任何其他相依資源。 監視 [活動] 索引標籤中的刪除，確認您的受控執行個體已刪除。 
1. 受控執行個體刪除後，請在資源群組中選取虛擬叢集  ，然後選擇 [刪除]  ，加以刪除。 在文字方塊中輸入 `yes` 以確認您要刪除資源，然後選取 [刪除]  。 
1. 刪除任何剩餘的資源。 在文字方塊中輸入 `yes` 以確認您要刪除資源，然後選取 [刪除]  。 
1. 選取 [刪除資源群組]  、輸入資源群組的名稱 `myResourceGroup`，然後選取 [刪除]  ，以刪除資源群組。 

## <a name="known-errors"></a>已知錯誤

### <a name="windows-logins-are-not-supported"></a>不支援 Windows 登入

`Exception Message: Windows logins are not supported in this version of SQL Server.`

代理程式已設定了 Windows 登入，必須改為使用 SQL Server 登入。 請使用**發行集屬性**的 [代理程式安全性]  頁面，將登入認證變更為 SQL Server 登入。 


### <a name="failed-to-connect-to-azure-storage"></a>無法連線至 Azure 儲存體


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 已取得 replstorage 的 Azure 儲存體連接字串 2019-11-19 02:21:05.07 正在連線至 Azure 檔案儲存體 '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 無法連線至 Azure 儲存體，並出現作業系統錯誤：53。


這很可能是因為 Azure 防火牆和 (或) Windows 防火牆已關閉連接埠 445。 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

在檔案共用的檔案路徑中使用正斜線 (而非反斜線)，可能會導致此錯誤。 這不構成問題：`\\replstorage.file.core.windows.net\replshare`這可能會導致作業系統 55 錯誤：`'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>無法連線至訂閱者

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

可能的解決方案：
- 確定連接埠 1433 已開啟。 
- 確定已在訂閱者上啟用 TCP/IP。 
- 確認建立訂閱者時使用了 DNS 名稱。 
- 確認您的虛擬網路已在私人 DNS 區域中正確連結。 
- 確認已正確設定您的 A 記錄。 
- 確認您的 VPN 對等互連已正確設定。 

### <a name="no-publications-to-which-you-can-subscribe"></a>您沒有可訂閱的發行集

使用**新增訂閱**精靈來新增訂閱時，您可能會發現 [發行集]  頁面上沒有任何資料庫和發行集列為可用的選項，且您可能會看到下列錯誤訊息：

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
雖然此錯誤訊息可能是正確的，且您連線到的發行者上實際上並沒有可用的發行集，或是您缺少足夠的權限，但這項錯誤也可能是舊版的 SQL Server Management Studio 所造成的。 請嘗試升級至 SQL Server Management Studio 18.0 或更新版本，將此問題從根本原因中排除。 


## <a name="next-steps"></a>後續步驟

### <a name="enable-security-features"></a>啟用安全性功能

請參閱下面的[受控執行個體功能的安全性功能](sql-database-managed-instance.md#azure-sql-database-security-features)文章，以取得完整的資料庫保護方式清單。 將會討論的安全性功能如下：

- [受控執行個體稽核](sql-database-managed-instance-auditing.md) 
- [一律加密](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [威脅偵測](sql-database-managed-instance-threat-detection.md) 
- [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)
- [資料列層級安全性](/sql/relational-databases/security/row-level-security) 
- [透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>受控執行個體功能

如需受控執行個體功能的完整概觀，請參閱：

> [!div class="nextstepaction"]
> [受控執行個體功能](sql-database-managed-instance.md)
