---
title: 在受控實例資料庫中設定複寫
description: 瞭解如何在 Azure SQL Database 受控實例發行者/散發者與受控實例訂閱者之間設定異動複寫。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 7356f627c8a85cb89f3900e1af84d5e0a7d4be17
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096214"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>在 Azure SQL Database 受控執行個體資料庫中設定複寫

異動複寫可讓您將資料從 SQL Server 資料庫或其他執行個體資料庫複寫到 Azure SQL Database 受控執行個體資料庫。 

本文說明如何設定受控實例發行者/散發者與受控實例訂閱者之間的複寫。 

![在兩個受控實例之間複寫](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

您也可以使用異動複寫，將 Azure SQL Database 受控實例中實例資料庫所做的變更推送至：

- SQL Server 資料庫。
- Azure SQL Database 中的單一資料庫。
- Azure SQL Database 彈性集區中的集區資料庫。
 
異動複寫在[Azure SQL Database 受控實例](sql-database-managed-instance.md)上處於公開預覽狀態。 受控執行個體可以裝載發行者、散發者和訂閱者資料庫。 如需可用設定的相關資訊，請參閱[異動複寫設定](sql-database-managed-instance-transactional-replication.md#common-configurations)。

  > [!NOTE]
  > - 本文旨在引導使用者從端對端設定 Azure 資料庫受控實例的複寫，從建立資源群組開始。 如果您已經部署受控實例，請直接跳到[步驟 4](#4---create-a-publisher-database)來建立發行者資料庫，如果您已經有發行者和訂閱者資料庫，而且準備好開始設定複寫，請略過步驟[6](#6---configure-distribution) 。  
  > - 這篇文章會在同一個受控實例上設定您的發行者和散發者。 若要將散發者放在個別的受控實例上，請參閱[設定 MI 發行者與 mi 散發者之間](sql-database-managed-instance-configure-replication-tutorial.md)的複寫教學課程。 

## <a name="requirements"></a>需求

將受控實例設定為「發行者」和/或「散發者」時，需要：

- 受控執行個體目前未參與異地複寫關聯性。
- 發行者受控實例與散發者和訂閱者位於相同的虛擬網路上，或已在所有三個實體的虛擬網路之間建立[vNet 對等互連](../virtual-network/tutorial-connect-virtual-networks-powershell.md)。 
- 連線會在複寫參與者之間使用 SQL 驗證。
- 複寫工作目錄的 Azure 儲存體帳戶共用。
- 埠445（TCP 輸出）已在 NSG 的安全性規則中開啟，以供受控實例存取 Azure 檔案共用。  如果您遇到「無法連線到 azure 儲存體 \<儲存體帳戶名稱 >，作業系統錯誤53」錯誤，您必須將輸出規則新增至適當 SQL 受控執行個體子網的 NSG。


 > [!NOTE]
 > Azure SQL Database 中的單一資料庫與集區資料庫只能是訂閱者。 


## <a name="features"></a>特性

支援：

- 混用 SQL Server 內部部署和 Azure SQL Database 中受控執行個體的異動與快照式複寫。
- 訂閱者可以位於內部部署 SQL Server 資料庫、Azure SQL Database 中的單一資料庫/受控實例，或 Azure SQL Database 彈性集區中的集區資料庫。
- 單向或雙向複寫。

Azure SQL Database 的受控執行個體中不支援下列功能：

- [可更新的訂閱](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)。
- 具有異動複寫的[主動式異地](sql-database-active-geo-replication.md)複寫。 使用[自動容錯移轉群組](sql-database-auto-failover-group.md)，而不是作用中異地複寫，但請注意，必須從主要受控實例[手動刪除](sql-database-managed-instance-transact-sql-information.md#replication)發行集，並在容錯移轉之後于次要受控實例上重新建立。  
 
## <a name="1---create-a-resource-group"></a>1-建立資源群組

使用[Azure 入口網站](https://portal.azure.com)來建立名稱為 `SQLMI-Repl`的資源群組。  

## <a name="2---create-managed-instances"></a>2-建立受控實例

使用[Azure 入口網站](https://portal.azure.com)在相同的虛擬網路和子網上建立兩個[受控實例](sql-database-managed-instance-create-tutorial-portal.md)。 例如，將兩個受控實例命名為：

- `sql-mi-pub` （以及隨機的部分字元）
- `sql-mi-sub` （以及隨機的部分字元）

您也需要[設定 AZURE VM，以](sql-database-managed-instance-configure-vm.md)連線到您 Azure SQL Database 的受控實例。 

## <a name="3---create-azure-storage-account"></a>3-建立 Azure 儲存體帳戶

為工作目錄[建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)，然後在儲存體帳戶內建立[檔案共用](../storage/files/storage-how-to-create-file-share.md)。 

複製下列格式的檔案共用路徑：`\\storage-account-name.file.core.windows.net\file-share-name`

範例： `\\replstorage.file.core.windows.net\replshare`

以下列格式複製儲存體存取金鑰： `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

範例： `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)。 

## <a name="4---create-a-publisher-database"></a>4-建立發行者資料庫

使用 SQL Server Management Studio 連接到您的 `sql-mi-pub` 受控實例，並執行下列 Transact-sql （T-sql）程式碼，以建立發行者資料庫：

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

## <a name="5---create-a-subscriber-database"></a>5-建立訂閱者資料庫

使用 SQL Server Management Studio 連接到您的 `sql-mi-sub` 受控實例，並執行下列 T-sql 程式碼來建立空白的訂閱者資料庫：

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

## <a name="6---configure-distribution"></a>6-設定散發

使用 SQL Server Management Studio 連接到您的 `sql-mi-pub` 受控實例，並執行下列 T-sql 程式碼來設定散發資料庫。 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-將發行者設定為使用散發者 

在您的發行者受控實例 `sql-mi-pub`上，將查詢執行變更為[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式，然後執行下列程式碼，向您的發行者註冊新的散發者。 

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
   > 請務必只針對 file_storage 參數使用反斜線（`\`）。 若使用正斜線 (`/`)，在連線至檔案共用時可能會導致錯誤。 

此腳本會在受控實例上設定本機發行者、加入連結的伺服器，並為 SQL Server Agent 建立一組作業。 

## <a name="8---create-publication-and-subscriber"></a>8-建立發行集和訂閱者

使用[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式，執行下列 t-sql 腳本來啟用資料庫的複寫，並設定發行者、散發者和訂閱者之間的複寫。 

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


-- Configure your log reaer agent
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

## <a name="9---modify-agent-parameters"></a>9-修改代理程式參數

Azure SQL Database 受控實例目前遇到一些與複寫代理程式連接的後端問題。 雖然此問題已解決，但因應措施是增加複寫代理程式的登入超時值。 

在發行者上執行下列 T-sql 命令，以增加登入超時： 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

再次執行下列 T-sql 命令，將登入超時設定為預設值（如果您需要這麼做）：

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

重新開機所有三個代理程式以套用這些變更。 

## <a name="10---test-replication"></a>10-測試複寫

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

若要卸載發行集，請執行下列 T-sql 命令：

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

若要從資料庫中移除複寫選項，請執行下列 T-sql 命令：

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

若要停用發行和散發，請執行下列 T-sql 命令：

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

您可以[從資源群組中刪除受控實例資源](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)，然後刪除資源群組 `SQLMI-Repl`，以清除您的 Azure 資源。 

   
## <a name="see-also"></a>另請參閱

- [異動複寫](sql-database-managed-instance-transactional-replication.md)
- [教學課程：設定 MI 發行者與 SQL Server 訂閱者之間的異動複寫](sql-database-managed-instance-configure-replication-tutorial.md)
- [什麼是受控執行個體？](sql-database-managed-instance.md)
