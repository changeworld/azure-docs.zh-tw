---
title: 異動複寫
description: 了解如何將 SQL Server 異動複寫搭配 Azure SQL Database 中的單一、集區和執行個體資料庫使用。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607602"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>搭配 Azure SQL Database 中單一、集區和執行個體資料庫使用的異動複寫

異動複寫是 Azure SQL Database 和 SQL Server 的功能，可讓您將 Azure SQL Database 或 SQL Server 中資料表的資料複寫到遠端資料庫上的資料表。 此功能可讓您同步處理不同資料庫中的多個資料表。

## <a name="when-to-use-transactional-replication"></a>使用異動複寫的時機

異動複寫對於下列情況非常有用：
- 發佈在資料庫中的一或多個資料表中所進行的變更，然後將它們散發到訂閱變更的 SQL Server 或 Azure SQL 資料庫。
- 讓數個散發資料庫處於同步狀態。
- 通過不斷發佈更改,將資料庫從一個 SQL Server 或託管實例遷移到另一個資料庫。

## <a name="overview"></a>概觀

下圖顯示異動複寫中的主要元件：  

![使用 SQL Database 的複寫](media/replication-to-sql-database/replication-to-sql-database.png)

**發行者**是執行個體或伺服器，它會藉由將更新傳送到散發者，來發行在某些資料表 (文章) 上的變更。 下列 SQL Server 版本支援從內部部署 SQL Server 發佈至任何 Azure SQL 資料庫：

- SQL Server 2019 (預覽)
- SQL Server 2016 至 SQL 2017
- SQL Server 2014 SP1 CU3 或更新版本 (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 或更新版本 (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- 針對其他不支援發行到 Azure 中物件的 SQL Server 版本，可以利用[重新發行](https://docs.microsoft.com/sql/relational-databases/replication/republish-data)資料方法將資料移動到版本較新的 SQL Server。 

**散發者**是執行個體過伺服器，它會從發行者收集文章中的變更，然後將變更散發到訂閱者。 分發伺服器可以是 Azure SQL 資料庫託管實例或 SQL Server(任何版本,只要它等於或高於發行者版本)。 

**訂閱者**是執行個體或伺服器，它會接收所收到的發行者所做的變更。 訂閱者可以是 Azure SQL Database 或 SQL Server 資料庫中的單一、集區和執行個體資料庫。 單一或集區資料庫上的訂閱者必須設定為發送訂閱者。 

| 角色 | 單一和集區資料庫 | 執行個體資料庫 |
| :----| :------------- | :--------------- |
| **發行者** | 否 | 是 | 
| **散發者** | 否 | 是|
| **提取訂閱者** | 否 | 是|
| **發送訂閱者**| 是 | 是|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > 當分發伺服器是實例資料庫且訂閱者不是時,不支援拉取訂閱。 

[複寫有不同類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)：


| 複寫 | 單一和集區資料庫 | 執行個體資料庫|
| :----| :------------- | :--------------- |
| [**標準交易**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 是 (僅作為訂閱者) | 是 | 
| [**快照式**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 是 (僅作為訂閱者) | 是|
| [**合併複製**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 否 | 否|
| [**點對點**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 否 | 否|
| [**雙向**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 否 | 是|
| [**可備份訂閱**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 否 | 否|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - 嘗試使用舊版設定複寫可能會導致錯誤號碼 MSSQL_REPL20084 (處理序無法連線到訂閱者。) 和 MSSQ_REPL40532 (無法開啟登入所要求的公開伺服器 \<名稱>。 登入失敗。)。
  > - 若要使用 Azure SQL Database 的所有功能，您必須使用最新版的 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>實體資料庫與本地端系統的可支援性矩陣
  實例資料庫的複製可支援性矩陣與本地 SQL Server 的複製支援矩陣相同。 
  
| **發行者**   | **散發者** | **訂閱者** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>需求

- 連線會在複寫參與者之間使用 SQL 驗證。 
- 與工作目錄共用且用於複寫的 Azure 儲存體帳戶。 
- 埠 445(TCP 出站)需要在託管實例子網的安全規則中打開才能訪問 Azure 文件共用。 
- 如果發佈伺服器/分發伺服器位於託管實例上且訂閱者不在託管實例上,則需要打開埠 1433(TCP 出站)。 您可能還需要將連接埠`allow_linkedserver_outbound`1433**目標服務標記**的託管實例 NSG 出`virtualnetwork`站安全`internet`規則從 變更為 。 
- 所有類型的複製參與者(發佈伺服器、分發伺服器、拉取訂閱伺服器和推送訂閱伺服器)都可以放置在託管實例上,但發佈者和分發伺服器必須同時位於雲中或同時位於本地。
- 如果發佈商、分發伺服器和/或訂閱者存在於不同的虛擬網路中,則必須在每個實體之間建立 VPN 對等互連,以便發佈者和分發伺服器之間存在 VPN 對等互連,並且/或分發伺服器和訂閱者之間存在 VPN 對等互連。 


>[!NOTE]
> - 如果分發伺服器是實例資料庫且訂閱者位於本地時,出站網路安全組 (NSG) 埠 445 被阻止,則連接到 Azure 儲存檔時可能會遇到錯誤 53。 [更新 vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)以解決此問題。 


### <a name="compare-data-sync-with-transactional-replication"></a>比較資料同步與異動複寫

| | 資料同步 | 異動複寫 |
|---|---|---|
| 優點 | - 主動-主動支援<br/>- 在內部部署與 Azure SQL Database 之間雙向進行 | - 更低的延遲性<br/>- 交易一致性<br/>- 移轉後重複使用現有的拓撲 |
| 缺點 | - 5 分鐘或更多的延遲<br/>- 無交易一致性<br/>- 更高的效能影響 | - 無法從 Azure SQL Database 單一資料庫或集區資料庫發佈<br/>- 高維護成本 |
| | | |

## <a name="common-configurations"></a>一般設定

一般情況下，發行者和散發者必須位在雲端或內部部署中。 不支援下列設定： 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>在託管實例上具有本地分發伺服器的伺服器

![單一執行個體作為發行者和散發者](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

發佈伺服器和分發伺服器在單個託管實例中配置,並將更改分發到其他託管實例、單個資料庫、池資料庫或本地 SQL Server。 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>在託管實例上具有遠端分發伺服器的伺服器

在此配置中,一個託管實例發佈對放置在另一個託管實例上的分發伺服器的更改,該實例可以服務許多源託管實例,並將更改分發到託管實例、單個資料庫、池資料庫或 SQL Server 上的一個或多個目標。

![發行者和散發者使用個別的執行個體](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

在兩個受控執行個體上設定發行者和散發者。 此設定存在一些約束: 

- 兩個託管實例都位於同一個 vNet 上。
- 這兩個受控執行個體位於相同的位置。


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>內部部署的發行者和散發者，以及單一、集區和執行個體資料庫上的訂閱者 

![Azure SQL DB 作為訂閱者](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
在此設定中，Azure SQL Database (單一、集區和執行個體資料庫) 是訂閱者。 此設定支援從內部部署移轉至 Azure。 如果訂閱者位於單一或集區資料庫上，則它必須處於發送模式。  

## <a name="with-failover-groups"></a>帶容錯移轉群組

如果在[故障轉移組中](sql-database-auto-failover-group.md)的**發行者**或**分發商**實例上啟用了異地複製,則託管實例管理員必須清理舊主伺服器上的所有發佈,並在故障轉移發生后在新主伺服器上重新配置它們。 在這種情況下,需要以下活動:

1. 停止在資料庫上運行的所有複製作業(如果有)。
2. 透過在發行者資料庫上執行以下文稿從發行者中刪除訂閱元資料:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. 從訂閱伺服器刪除訂閱元數據。 訂閱伺服器上的訂閱資料庫上執行以下文稿:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. 通過在已發佈的資料庫中運行以下文稿,強制從發行者中刪除所有複製物件:

   ```sql
   EXEC sp_removedbreplication
   ```

1. 強制將舊分發伺服器從原始主實例中刪除(如果失敗返回以前具有分發伺服器的舊主資料庫)。 在舊分發伺服器託管實體的主資料庫上執行以下文稿:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

如果在故障轉移組中的**訂戶**實例上啟用了異地複製,則應將發佈配置為連接到訂閱伺服器託管實例的故障轉移組偵聽器終結點。 在發生故障轉移時,託管實例管理員的後續操作取決於發生的故障轉移的類型: 

- 對於沒有數據丟失的故障轉移,複製將在故障轉移後繼續工作。 
- 對於具有數據丟失的故障轉移,複製也將有效。 它將再次複製丟失的更改。 
- 對於具有數據丟失的故障轉移,但數據丟失超出分發資料庫保留期,託管實例管理員將需要重新初始化訂閱資料庫。 

## <a name="next-steps"></a>後續步驟

- [設定 MI 發行者和訂閱者之間的複製](replication-with-sql-database-managed-instance.md)
- [設定 MI 發行者、MI 分發伺服器和 SQL Server 訂閱者之間的複製](sql-database-managed-instance-configure-replication-tutorial.md)
- [建立出版物](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)。
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription)，方法是使用 Azure SQL Database 伺服器名稱作為訂閱者 (例如 `N'azuresqldbdns.database.windows.net`)，並使用 Azure SQL Database 名稱作為目的地資料庫 (例如 **AdventureWorks**)。 )


有關配置事務複製的詳細資訊,請參閱以下教程:



## <a name="see-also"></a>另請參閱  

- [使用 MI 和故障轉移群組的複製](sql-database-managed-instance-transact-sql-information.md#replication)
- [複寫至 SQL Database](replication-to-sql-database.md)
- [複製到託管實體](replication-with-sql-database-managed-instance.md)
- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [複寫的類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (複寫)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化訂閱](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
