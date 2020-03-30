---
title: 在託管實例資料庫中配置複製
description: 瞭解如何在 Azure SQL 資料庫託管實例發行者/分發伺服器和託管實例訂閱伺服器之間配置異動複寫。
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
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299068"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>在 Azure SQL Database 受控執行個體資料庫中設定複寫

異動複寫可讓您將資料從 SQL Server 資料庫或其他執行個體資料庫複寫到 Azure SQL Database 受控執行個體資料庫。 

本文演示如何配置託管實例發行者/分發伺服器和託管實例訂閱者之間的複製。 

![在兩個託管實例之間複製](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

您還可以使用異動複寫將 Azure SQL 資料庫託管實例中的實例資料庫中所做的更改推送到：

- SQL 伺服器資料庫。
- Azure SQL 資料庫中的單個資料庫。
- Azure SQL 資料庫彈性池中的池資料庫。
 
異動複寫在 Azure SQL[資料庫託管實例](sql-database-managed-instance.md)上處於公共預覽中。 受控執行個體可以裝載發行者、散發者和訂閱者資料庫。 有關可用配置[，請參閱異動複寫配置](sql-database-managed-instance-transactional-replication.md#common-configurations)。

  > [!NOTE]
  > - 本文旨在指導使用者從端到端使用 Azure 資料庫託管實例配置複製，從創建資源組開始。 如果已部署託管實例，請跳到[步驟 4](#4---create-a-publisher-database)以創建發行者資料庫，或者[跳過步驟 6（](#6---configure-distribution)如果您已有發行者和訂閱者資料庫，並且已準備好開始配置複製）。  
  > - 本文在同一託管實例上配置發行者和分發伺服器。 要將分發伺服器放在單獨的 manged 實例上，請參閱[MI 發行者和 MI 分發伺服器 之間配置複製的](sql-database-managed-instance-configure-replication-tutorial.md)教程。 

## <a name="requirements"></a>需求

將託管實例配置為發行者和/或分發伺服器需要：

- 發行者託管實例與分發伺服器和訂閱者位於同一虛擬網路上，或者在所有三個實體的虛擬網路之間建立[vNet 對等互連](../virtual-network/tutorial-connect-virtual-networks-powershell.md)。 
- 連線會在複寫參與者之間使用 SQL 驗證。
- 複寫工作目錄的 Azure 儲存體帳戶共用。
- 埠 445（TCP 出站）在 NSG 的安全規則中為託管實例打開以訪問 Azure 檔共用。  如果遇到錯誤"無法連接到 azure 存儲\<存儲帳戶名稱> os 錯誤 53"，則需要向相應的 SQL 託管實例子網的 NSG 添加出站規則。


 > [!NOTE]
 > Azure SQL Database 中的單一資料庫與集區資料庫只能是訂閱者。 


## <a name="features"></a>特性

支援：

- 混用 SQL Server 內部部署和 Azure SQL Database 中受控執行個體的異動與快照式複寫。
- 訂閱者可以位於本地 SQL Server 資料庫中、Azure SQL 資料庫中的單個資料庫/託管實例或 Azure SQL 資料庫彈性池中的池化資料庫。
- 單向或雙向複寫。

Azure SQL Database 的受控執行個體中不支援下列功能：

- [可備份訂閱](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)。
- [使用異動複寫進行活動異地複製](sql-database-active-geo-replication.md)。 使用[自動容錯移轉組](sql-database-auto-failover-group.md)，而不是活動異地複製，但請注意，必須手動從主託管實例[中刪除](sql-database-managed-instance-transact-sql-information.md#replication)發佈，並在容錯移轉後在輔助託管實例上重新創建。  
 
## <a name="1---create-a-resource-group"></a>1 - 創建資源組

使用[Azure 門戶](https://portal.azure.com)創建名稱 的資源組`SQLMI-Repl`。  

## <a name="2---create-managed-instances"></a>2 - 創建託管實例

使用[Azure 門戶](https://portal.azure.com)在同一虛擬網路和子網上創建兩[個託管實例](sql-database-managed-instance-create-tutorial-portal.md)。 例如，為兩個託管實例命名：

- `sql-mi-pub`（以及一些用於隨機化的字元）
- `sql-mi-sub`（以及一些用於隨機化的字元）

您還需要[配置 Azure VM 以連接到](sql-database-managed-instance-configure-vm.md)Azure SQL 資料庫託管實例。 

## <a name="3---create-azure-storage-account"></a>3 - 創建 Azure 存儲帳戶

為工作目錄[建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)，然後在儲存體帳戶內建立[檔案共用](../storage/files/storage-how-to-create-file-share.md)。 

複製下列格式的檔案共用路徑：`\\storage-account-name.file.core.windows.net\file-share-name`

範例： `\\replstorage.file.core.windows.net\replshare`

以以下格式複製存儲訪問金鑰：`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

範例： `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)。 

## <a name="4---create-a-publisher-database"></a>4 - 創建發行者資料庫

使用 SQL`sql-mi-pub`伺服器管理工作室連接到託管實例，並運行以下 Transact-SQL （T-SQL） 代碼以創建發行者資料庫：

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

## <a name="5---create-a-subscriber-database"></a>5 - 創建訂閱者資料庫

使用 SQL`sql-mi-sub`伺服器管理工作室連接到託管實例，並運行以下 T-SQL 代碼以創建空訂閱者資料庫：

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

## <a name="6---configure-distribution"></a>6 - 配置分發

使用 SQL`sql-mi-pub`伺服器管理工作室連接到託管實例，並運行以下 T-SQL 代碼來配置散發資料庫。 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - 將發佈伺服器配置為使用分發伺服器 

在發行者託管實例`sql-mi-pub`上，將查詢執行更改為[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式，並運行以下代碼以向發行者註冊新分發伺服器。 

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
   > 請確保對file_storage參數僅使用斜杠`\`（ ） 。 若使用正斜線 (`/`)，在連線至檔案共用時可能會導致錯誤。 

此腳本在託管實例上配置本地發行者，添加連結伺服器，並為 SQL Server 代理創建一組作業。 

## <a name="8---create-publication-and-subscriber"></a>8 - 創建出版物和訂閱者

使用[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式，運行以下 T-SQL 腳本以啟用資料庫複製，並在發行者、分發伺服器和訂閱者之間配置複製。 

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

## <a name="9---modify-agent-parameters"></a>9 - 修改代理參數

Azure SQL 資料庫託管實例當前遇到與複製代理連接的一些後端問題。 在解決此問題時，增加複製代理的登錄超時值的解決方法。 

在發佈伺服器上運行以下 T-SQL 命令以增加登錄超時： 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

如果需要，再次運行以下 T-SQL 命令，將登錄超時設置回預設值：

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

重新開機所有三個代理以應用這些更改。 

## <a name="10---test-replication"></a>10 - 測試複製

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

要刪除發佈，運行以下 T-SQL 命令：

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

要從資料庫中刪除複製選項，請運行以下 T-SQL 命令：

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

要禁用發佈和分發，請運行以下 T-SQL 命令：

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

您可以通過[從資源組中刪除託管實例資源](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)，然後刪除資源組`SQLMI-Repl`來清理 Azure 資源。 

   
## <a name="see-also"></a>另請參閱

- [異動複寫](sql-database-managed-instance-transactional-replication.md)
- [教程：配置 MI 發行者和 SQL 伺服器訂閱者之間的異動複寫](sql-database-managed-instance-configure-replication-tutorial.md)
- [什麼是受控執行個體？](sql-database-managed-instance.md)
