---
title: Azure SQL Server 複寫至 Azure SQL Database
description: 您可以在單向交易式或快照式複寫拓撲中，將 Azure SQL Database 中的資料庫設定為發送訂閱者。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780949"
---
# <a name="replication-to-azure-sql-database"></a>複寫至 Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

您可以在單向交易式或快照式複寫拓撲中，將 Azure SQL Database 設定為發送訂閱者。

> [!NOTE]
> 本文描述如何在 Azure SQL Database 中使用 [異動複寫](/sql/relational-databases/replication/transactional/transactional-replication) 。 它與 [主動式異地](./active-geo-replication-overview.md)複寫無關，這是一個 Azure SQL Database 的功能，可讓您建立個別資料庫的完整可讀取複本。

## <a name="supported-configurations"></a>支援的設定
  
- Azure SQL Database 只能是 SQL Server 發行者和散發者的發送訂閱者。  
- 作為「發行者」和/或「散發者」 SQL Server 實例可以是 [在內部部署](https://www.microsoft.com/sql-server/sql-server-downloads)環境中執行的 SQL Server 實例、 [azure SQL 受控執行個體](../managed-instance/instance-create-quickstart.md)，或是在 [雲端的 azure 虛擬機器上](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)執行的 SQL Server 實例。 
- 散發資料庫和複寫代理程式不能放在 Azure SQL Database 的資料庫上。  
- 支援[快照](/sql/relational-databases/replication/snapshot-replication)式與[單向事務](/sql/relational-databases/replication/transactional/transactional-replication)複製。 不支援點對點異動複寫和合併式複寫。

### <a name="versions"></a>版本  

若要成功複寫至 Azure SQL Database 中的資料庫，SQL Server 發行者和散發者必須至少使用 () 下列其中一個版本：

下列 SQL Server 版本支援從 SQL Server 資料庫發佈至任何 Azure SQL Database：

- SQL Server 2016 和更新版本
- SQL Server 2014 [RTM CU10 (12.0.4427.24) ](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) 或 [SP1 CU3 (12.0.2556.4) ](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1) ](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) 或 [SP3 (11.0.6020.0) ](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> 嘗試使用不支援的版本來設定複寫可能會導致錯誤號碼 MSSQL_REPL20084 (處理常式無法連接到訂閱者。 ) 和 MSSQL_REPL40532 (無法開啟 \<name> 登入所要求的伺服器。 登入失敗。)。  

若要使用 Azure SQL Database 的所有功能，您必須使用最新版的 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)。  

### <a name="types-of-replication"></a>複寫類型

[複寫有不同類型](/sql/relational-databases/replication/types-of-replication)：

| 複寫 | Azure SQL Database | Azure SQL 受控執行個體 |
| :----| :------------- | :--------------- |
| [**標準交易式**](/sql/relational-databases/replication/transactional/transactional-replication) | 是 (僅作為訂閱者) | 是 | 
| [**快照式**](/sql/relational-databases/replication/snapshot-replication) | 是 (僅作為訂閱者) | 是|
| [**合併式複寫**](/sql/relational-databases/replication/merge/merge-replication) | 否 | 否|
| [**對等**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 否 | 否|
| [**雙向**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 否 | 是|
| [**可更新訂閱**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 否 | 否|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>備註

- 僅支援對 Azure SQL Database 的發送訂閱。  
- 若要設定複寫，請使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或在發行者上執行 Transact-SQL 陳述式。 您無法使用 Azure 入口網站來設定複寫。  
- 複寫只能使用 SQL Server authentication 登入來連接 Azure SQL Database。
- 複寫的資料表都必須有主索引鍵。  
- 您必須具有現有的 Azure 訂用帳戶。  
- Azure SQL Database 訂閱者可以在任何區域中。  
- SQL Server 上的單一發行集可同時支援 Azure SQL Database 和 SQL Server (內部部署和 Azure 虛擬機器中的 SQL Server) 訂閱者。  
- 複寫管理、監視和疑難排解都必須從 SQL Server 執行，而不是 Azure SQL Database。  
- 在 SQL Database 的 **sp_addsubscription** 中僅支援 `@subscriber_type = 0`。  
- Azure SQL Database 不支援雙向、即時、可更新或點對點複寫。

## <a name="replication-architecture"></a>複寫架構  

![下圖顯示具有 Azure SQL Database 的複寫架構，其中包含不同區域中的數個訂閱者叢集，以及內部部署 Azure 虛擬機器，其中包含可連接至遠端叢集的發行者、Logread 可執行檔和散發者可執行檔。](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>案例  

### <a name="typical-replication-scenario"></a>一般複寫案例  

1. 在 SQL Server 資料庫上建立異動複寫發行集。  
2. 在 SQL Server 使用「 **新增訂閱嚮導** 」或 transact-sql 語句，以建立 Azure SQL Database 的發送至訂用帳戶。  
3. 使用 Azure SQL Database 中的單一和集區資料庫，初始資料集是由快照集代理程式建立且由散發代理程式散發並套用的快照集。 使用 SQL 受控執行個體發行者，您也可以使用資料庫備份來植入 Azure SQL Database 訂閱者。

### <a name="data-migration-scenario"></a>資料移轉案例  

1. 使用異動複寫，將資料從 SQL Server 資料庫複寫到 Azure SQL Database。  
2. 重新導向用戶端或仲介層應用程式，以更新資料庫複本。  
3. 停止更新 SQL Server 版本的資料表，並移除發行集。  

## <a name="limitations"></a>限制

Azure SQL Database 訂閱不支援下列選項：

- 複製檔案群組關聯  
- 複製資料表資料分割配置  
- 複製索引資料分割配置  
- 複製使用者定義的統計資料  
- 複製預設繫結  
- 複製規則繫結  
- 複製全文檢索索引  
- 複製 XML XSD  
- 複製 XML 索引  
- 複製權限  
- 複製空間索引  
- 複製篩選的索引  
- 複製資料壓縮屬性  
- 複製疏鬆資料行屬性  
- 將 filestream 轉換為 MAX 資料類型  
- 將 hierarchyid 轉換為 MAX 資料類型  
- 將 spatial 轉換為 MAX 資料類型  
- 複製擴充屬性  

### <a name="limitations-to-be-determined"></a>待決定的限制

- 複製定序  
- SP 的序列式交易執行  

## <a name="examples"></a>範例

建立發行集和發送訂閱。 如需詳細資訊，請參閱
  
- [建立發行集](/sql/relational-databases/replication/publish/create-a-publication)
- 使用伺服器名稱做為訂閱者來 [建立發送訂閱](/sql/relational-databases/replication/create-a-push-subscription/) (例如 **N'azuresqldbdns** ）) 和 Azure SQL Database 名稱做為目的地資料庫 (例如 **AdventureWorks** ) 。  

## <a name="see-also"></a>另請參閱  

- [異動複寫](../managed-instance/replication-transactional-overview.md)
- [建立發行集](/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](/sql/relational-databases/replication/create-a-push-subscription/)
- [複寫的類型](/sql/relational-databases/replication/types-of-replication)
- [監視 (複寫)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化訂閱](/sql/relational-databases/replication/initialize-a-subscription)