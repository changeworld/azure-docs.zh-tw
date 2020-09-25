---
title: 異動複寫
titleSuffix: Azure SQL Managed Instance
description: 瞭解如何搭配 Azure SQL 受控執行個體 (Preview) 使用 SQL Server 異動複寫。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: a335f6ac015397ba2b2634d0d604c194a768260a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283202"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>使用 Azure SQL 受控執行個體 (Preview 的異動複寫) 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

異動複寫是 Azure SQL 受控執行個體和 SQL Server 的功能，可讓您將 Azure SQL 受控執行個體或 SQL Server 實例中資料表的資料複寫到遠端資料庫上的資料表。 此功能可讓您同步處理不同資料庫中的多個資料表。 

SQL 受控執行個體的異動複寫目前處於公開預覽狀態。 

## <a name="overview"></a>概觀

您可以使用異動複寫，將 Azure SQL 受控執行個體中所做的變更推送至：

- SQL Server 內部部署或 Azure VM 上的資料庫
- Azure SQL Database 中的資料庫
- Azure SQL 受控執行個體中的實例資料庫

  > [!NOTE]
  > 若要使用 Azure SQL 受控執行個體的所有功能，您必須使用最新版本的 [SQL Server Management Studio (SSMS) ](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools (SSDT) ](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。

### <a name="components"></a>元件

異動複寫中的重要元件是「 **發行者**」 **、「** 散發者」和「 **訂閱者**」，如下圖所示：  

![使用 SQL Database 的複寫](./media/replication-transactional-overview/replication-to-sql-database.png)

| 角色 | Azure SQL Database | Azure SQL 受控執行個體 |
| :----| :------------- | :--------------- |
| **發行者** | 否 | 是 |
| **散發者** | 否 | 是|
| **提取訂閱者** | 否 | 是|
| **發送訂閱者**| 是 | 是|
| &nbsp; | &nbsp; | &nbsp; |

發行者會將更新傳送至「散發 **者** 」，藉以將某些資料表上所做的變更發行 (的發行項) 。 發行者可以是 Azure SQL 受控執行個體或 SQL Server 實例。

散發者會從 **發行者收集發行** 項的變更，並將它們散發給訂閱者。 散發者可以是 Azure SQL 受控執行個體或 SQL Server 實例 (任何版本，只要它等於或高於發行者版本) 即可。

**訂閱**者會收到在發行者上所做的變更。 SQL Server 實例和 Azure SQL 受控執行個體可以是發送和提取訂閱者，不過當散發者是 Azure SQL 受控執行個體且訂閱者不支援時，不支援提取訂閱。 Azure SQL Database 中的資料庫只能是發送訂閱者。

Azure SQL 受控執行個體可支援從下列版本的 SQL Server 訂閱者：

- SQL Server 2016 及更新版本
- SQL Server 2014 [RTM CU10 (12.0.4427.24) ](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) 或 [SP1 CU3 (12.0.2556.4) ](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1) ](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) 或 [SP3 (11.0.6020.0) ](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - 針對其他不支援發行到 Azure 中物件的 SQL Server 版本，可以利用[重新發行](https://docs.microsoft.com/sql/relational-databases/replication/republish-data)資料方法將資料移動到版本較新的 SQL Server。
   > - 嘗試使用舊版設定複寫可能會導致錯誤號碼 MSSQL_REPL20084 (處理序無法連線到訂閱者。) 和 MSSQ_REPL40532 (無法開啟登入所要求的公開伺服器 \<name>。 登入失敗。)。

### <a name="types-of-replication"></a>複寫類型

[複寫有不同類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)：

| 複寫 | Azure SQL Database | Azure SQL 受控執行個體 |
| :----| :------------- | :--------------- |
| [**標準交易式**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 是 (僅作為訂閱者) | 是 |
| [**快照式**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 是 (僅作為訂閱者) | 是|
| [**合併式複寫**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 否 | 否|
| [**對等**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 否 | 否|
| [**雙向**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 否 | 是|
| [**可更新訂閱**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 否 | 否|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>支援性對照表

  適用于 Azure SQL 受控執行個體的異動複寫可支援性對照表與 SQL Server 相同。
  
| **發行者**   | **散發者** | **訂閱者** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>使用時機

異動複寫對於下列情況非常有用：

- 在資料庫中發行一個或多個資料表的變更，並將其散發至 SQL Server 實例中的一或多個資料庫，或訂閱變更的 Azure SQL Database。
- 讓數個散發資料庫處於同步狀態。
- 藉由持續發佈變更，將資料庫從一個 SQL Server 實例或 Azure SQL 受控執行個體遷移至另一個資料庫。

### <a name="compare-data-sync-with-transactional-replication"></a>比較資料同步與異動複寫

| 類別 | 資料同步 | 異動複寫 |
|---|---|---|
| 優點 | - 主動-主動支援<br/>- 在內部部署與 Azure SQL Database 之間雙向進行 | - 更低的延遲性<br/>- 交易一致性<br/>- 移轉後重複使用現有的拓撲 |
| 缺點 | - 5 分鐘或更多的延遲<br/>- 無交易一致性<br/>- 更高的效能影響 | -無法從 Azure SQL Database 發行 <br/>- 高維護成本 |

## <a name="common-configurations"></a>一般設定

一般情況下，發行者和散發者必須位在雲端或內部部署中。 不支援下列設定：

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>在 SQL 受控執行個體上使用本機散發者的發行者

![單一執行個體作為發行者和散發者](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

發行者和散發者設定在單一 SQL 受控執行個體內，並將變更散發到另一個 SQL 受控執行個體、SQL Database 或 SQL Server 實例。

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>在 SQL 受控執行個體上使用遠端散發者的發行者

在此設定中，一個受控實例會將變更發佈至位於另一個 SQL 受控執行個體的散發者，以提供許多來源 SQL 受控實例，並將變更散發到 Azure SQL Database、Azure SQL 受控執行個體或 SQL Server 上的一或多個目標。

![發行者和散發者使用個別的執行個體](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

在兩個受控執行個體上設定發行者和散發者。 這種設定有一些限制：

- 這兩個受控實例都位於相同的 vNet 上。
- 這兩個受控執行個體位於相同的位置。

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>具有遠端訂閱者的內部部署發行者/散發者

![Azure SQL Database 為訂閱者](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

在此設定中，Azure SQL Database 或 Azure SQL 受控執行個體中的資料庫是「訂閱者」。 此設定支援從內部部署移轉至 Azure。 如果訂閱者是 Azure SQL Database 中的資料庫，則必須處於推送模式。  

## <a name="requirements"></a>需求

- 使用 SQL 驗證進行複寫參與者之間的連接。
- 針對複寫所使用的工作目錄使用 Azure 儲存體帳戶共用。
- 在子網安全性規則中開啟 TCP 輸出埠445，以存取 Azure 檔案共用。
- 當 SQL 受控執行個體是發行者/散發者，而訂閱者不是時，開啟 TCP 輸出通訊埠1433。 您也可能需要將 `allow_linkedserver_outbound` 埠 1433 **目的地服務** 標籤的 SQL 受控執行個體 NSG 輸出安全性規則從變更為 `virtualnetwork` `internet` 。
- 將「發行者」和「散發者」放在雲端或內部部署中。
- 如果虛擬網路不同，請在複寫參與者的虛擬網路之間設定 VPN 對等互連。

> [!NOTE]
> 當「散發者」為「Azure SQL 受控執行個體資料庫」且「訂閱者」為內部部署時，當您連線到 Azure 儲存體檔案時，可能會遇到錯誤53（如果輸出網路安全性群組 (NSG) 埠445）。 [更新 VNET NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) 以解決此問題。

## <a name="with-failover-groups"></a>使用容錯移轉群組

使用異動複寫的 SQL 受控執行個體不支援[主動式異地](../database/active-geo-replication-overview.md)複寫。 您可以使用 [自動容錯移轉群組](../database/auto-failover-group-overview.md)，而不是使用主動式異地複寫，但請注意，在容錯移轉之後，必須從主要受控實例 [手動刪除](transact-sql-tsql-differences-sql-server.md#replication) 發行集，並在次要 SQL 受控執行個體上重新建立。

如果在[容錯移轉群組](../database/auto-failover-group-overview.md)中的「**發行者**」**或「** 散發者」 SQL 受控執行個體上啟用「異地複寫」，則 SQL 受控執行個體系統管理員必須清除舊主要複本上的所有發行集，並在發生容錯移轉之後，在新的主資料庫上重新設定它們。 以下是此案例中所需的活動：

1. 停止在資料庫上執行的所有複寫作業（如果有的話）。
1. 在發行者資料庫上執行下列腳本，以卸載發行者的訂閱中繼資料：

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. 從訂閱者卸載訂閱中繼資料。 在訂閱者 SQL 受控執行個體上的訂閱資料庫上執行下列腳本：

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. 在已發行的資料庫中執行下列腳本，以強制卸載「發行者」的所有複寫物件：

   ```sql
   EXEC sp_removedbreplication
   ```

1. 如果容錯移轉回舊的主要複本（用來讓散發者) ），請從原始的主要 SQL 受控執行個體強制卸載舊的散發者 (。 在舊的散發者 SQL 受控執行個體的 master 資料庫上執行下列腳本：

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

如果在容錯移轉群組的 **訂閱者** 實例上啟用異地複寫，則應該將發行集設定為連接到訂閱者受控實例的容錯移轉群組接聽程式端點。 發生容錯移轉時，受管理的實例系統管理員的後續動作取決於發生的容錯移轉類型：

- 針對不會遺失資料的容錯移轉，複寫將在容錯移轉之後繼續運作。
- 針對資料遺失的容錯移轉，複寫也會正常運作。 它會再次複寫遺失的變更。
- 如果是資料遺失的容錯移轉，但資料遺失超出散發資料庫保留期限，SQL 受控執行個體系統管理員就必須重新初始化訂閱資料庫。

## <a name="next-steps"></a>後續步驟

如需有關設定異動複寫的詳細資訊，請參閱下列教學課程：

- [設定 SQL 受控執行個體發行者和訂閱者之間的複寫](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [設定 SQL 受控執行個體發行者、SQL 受控執行個體散發者和 SQL Server 訂閱者之間的複寫](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [建立發行](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)集。
- 使用伺服器名稱做為訂閱者來[建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) (例如， `N'azuresqldbdns.database.windows.net` 將資料庫 Azure SQL Database 名稱做為目的地資料庫 (例如**Adventureworks**。 )

## <a name="see-also"></a>另請參閱  

- [使用 SQL 受控執行個體和容錯移轉群組進行複寫](transact-sql-tsql-differences-sql-server.md#replication)
- [複寫至 SQL Database](../database/replication-to-sql-database.md)
- [複寫至受控實例](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [複寫的類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (複寫)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化訂閱](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
