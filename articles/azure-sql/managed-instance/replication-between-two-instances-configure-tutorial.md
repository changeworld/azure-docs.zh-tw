---
title: 設定受控執行個體之間的複寫
titleSuffix: Azure SQL Managed Instance
description: 本教學課程將說明如何設定 Azure SQL 受控執行個體發行者/散發者與 SQL 受控執行個體訂閱者之間的異動複寫。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 5948f7adedfa430d7d0dac570910f662846464c6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790860"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>教學課程：設定兩個受控執行個體之間的複寫

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

異動複寫可讓您將資料從某個資料庫複寫至裝載於 SQL Server 或 [Azure SQL 受控執行個體](sql-managed-instance-paas-overview.md)上的另一個資料庫。 SQL 受控執行個體可以是複寫拓撲中的發行者、散發者或訂閱者。 如需可用設定的相關資訊，請參閱[異動複寫設定](replication-transactional-overview.md#common-configurations)。 

SQL 受控執行個體的異動複寫目前處於公開預覽狀態。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 將受控執行個體設定為複寫發行者和散發者。
> - 將受控執行個體設定為複寫散發者。

![在兩個受控執行個體之間進行複寫](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

本教學課程適用於有經驗的對象，並假設使用者熟知如何部署和連線至受控執行個體和 Azure 中的 SQL Server VM。 


> [!NOTE]
> - 本文說明如何在 Azure SQL 受控執行個體中使用[異動複寫](/sql/relational-databases/replication/transactional/transactional-replication)。 這與[容錯移轉群組](../database/auto-failover-group-overview.md)無關，後者是一種 Azure SQL 受控執行個體功能，可讓您建立個別執行個體的完整可讀取複本。 在設定[容錯移轉群組的異動複寫](replication-transactional-overview.md#with-failover-groups)時，還要考慮其他事項。



## <a name="requirements"></a>需求

要將 SQL 受控執行個體設定作為發行者和 (或) 散發者，有下列必要條件：

- 發行者受控執行個體位於與散發者與訂閱者相同的虛擬網路上，或是這三個實體的虛擬網路之間已設定 [VPN 閘道](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。 
- 連線會在複寫參與者之間使用 SQL 驗證。
- 複寫工作目錄的 Azure 儲存體帳戶共用。
- NSG 的安全性規則中應開啟連接埠 445 (TCP 輸出)，讓受控執行個體能夠存取 Azure 檔案共用。  如果發生 `failed to connect to azure storage \<storage account name> with os error 53` 錯誤，則必須將輸出規則新增至適當 SQL 受控執行個體子網路的 NSG。

## <a name="1---create-a-resource-group"></a>1 - 建立資源群組

使用 [Azure 入口網站](https://portal.azure.com)建立名稱為 `SQLMI-Repl` 的資源群組。  

## <a name="2---create-managed-instances"></a>2 - 建立受控執行個體

使用 [Azure 入口網站](https://portal.azure.com)，在相同的虛擬網路和子網路上建立兩個 [SQL 受控執行個體](instance-create-quickstart.md)。 例如，將兩個受控執行個體命名為：

- `sql-mi-pub` (加上一些隨機的字元)
- `sql-mi-sub` (加上一些隨機的字元)

您也需要[設定 Azure VM](connect-vm-instance-configure.md) 以連線至受控執行個體。 

## <a name="3---create-an-azure-storage-account"></a>3 - 建立 Azure 儲存體帳戶

為工作目錄[建立 Azure 儲存體帳戶](../../storage/common/storage-account-create.md#create-a-storage-account)，然後在儲存體帳戶內建立[檔案共用](../../storage/files/storage-how-to-create-file-share.md)。 

複製下列格式的檔案共用路徑：`\\storage-account-name.file.core.windows.net\file-share-name`

範例： `\\replstorage.file.core.windows.net\replshare`

複製下列格式的儲存體存取金鑰：`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

範例： `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../../storage/common/storage-account-keys-manage.md)。 

## <a name="4---create-a-publisher-database"></a>4 - 建立發行者資料庫

使用 SQL Server Management Studio 連線至您的 `sql-mi-pub` 受控執行個體，並執行下列 Transact-SQL (T-SQL) 程式碼以建立發行者資料庫：

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
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

## <a name="5---create-a-subscriber-database"></a>5 - 建立訂閱者資料庫

使用 SQL Server Management Studio 連線至您的 `sql-mi-sub` 受控執行個體，並執行下列 T-SQL 程式碼以建立空的訂閱者資料庫：

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - 設定散發

使用 SQL Server Management Studio 連線至您的 `sql-mi-pub` 受控執行個體，並執行下列 T-SQL 程式碼以設定散發資料庫。

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - 設定發行者以使用散發者

在您的發行者 SQL 受控執行個體 `sql-mi-pub` 上，將查詢執行變更為 [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 模式，並執行下列程式碼，以向發行者註冊新的散發者。

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > 對於 file_storage 參數請務必使用反斜線 (`\`)。 若使用正斜線 (`/`)，在連線至檔案共用時可能會導致錯誤。

此指令碼會在受控執行個體上設定本機發行者、新增連結的伺服器，以及為 SQL Server 代理程式建立一組作業。

## <a name="8---create-publication-and-subscriber"></a>8 - 建立發行集和訂閱者

使用 [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 模式執行下列 T-SQL 指令碼，以啟用資料庫的複寫，並設定發行者、散發者與訂閱者之間的複寫。

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reader agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - 修改代理程式參數

Azure SQL 受控執行個體目前在與複寫代理程式連線方面有一些後端問題。 解決此問題時，因應措施是增加複寫代理程式的登入逾時值。

請對發行者執行下列 T-SQL 命令，以增加登入逾時：

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

如有必要，您可以再次執行下列 T-SQL 命令，將登入逾時重新設為預設值：

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

將三個代理程式都重新啟動，以套用這些變更。

## <a name="10---test-replication"></a>10 - 測試複寫

設定複寫後，您可以在發行者上插入新項目，並監看這些變更傳播到訂閱者，藉以測試複寫。

執行下列 T-SQL 程式碼片段，以檢視訂閱者上的資料列：

```sql
select * from dbo.ReplTest
```

執行下列 T-SQL 程式碼片段，在發行者上插入其他資料列，然後再次查看訂閱者上的資料列。

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>清除資源

若要卸除發行集，請執行下列 T-SQL 命令：

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

若要從資料庫中移除複寫選項，請執行下列 T-SQL 命令：

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

若要停用發佈和散發，請執行下列 T-SQL 命令：

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

您可以[從資源群組中刪除 SQL 受控執行個體資源](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources)，然後刪除資源群組 `SQLMI-Repl`，藉以清除您的 Azure 資源。 

## <a name="next-steps"></a>後續步驟

您也可以深入了解 [Azure SQL 受控執行個體之間的異動複寫](replication-transactional-overview.md)，或了解如何在 [SQL 受控執行個體發行者/散發者與 Azure VM 訂閱者上的 SQL 之間](replication-two-instances-and-sql-server-configure-tutorial.md)設定複寫。