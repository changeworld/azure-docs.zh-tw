---
title: 複寫
description: 了解如何搭配 Azure SQL Database 單一資料庫和彈性集區中的資料庫使用 SQL Server 複寫
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f28269b067ee98d69a97799911fd2d84a7f91e34
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381155"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>複寫至 SQL Database 單一資料庫與集區資料庫

可以將 SQL Server 複寫設定為 Azure SQL Database 中 [SQL Database 伺服器](sql-database-servers.md)上的單一資料庫與集區資料庫。  

## <a name="supported-configurations"></a>**支援的設定：**
  
- SQL Server 可以是在內部部署執行的 SQL Server 執行個體，或是在雲端 Azure 虛擬機器中執行的 SQL Server 執行個體。 如需詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)。  
- Azure SQL 資料庫必須是 SQL Server 發行者的發送訂閱者。  
- 散發資料庫和複寫代理程式不能置於 Azure SQL 資料庫上。  
- 支援快照集和單向異動複寫。 不支援點對點異動複寫和合併式複寫。
- Azure SQL Database 受控執行個體上的複寫功能現供公開預覽。 受控執行個體可以裝載發行者、散發者和訂閱者資料庫。 如需詳細資訊，請參閱[使用 SQL Database 受控執行個體進行複寫](replication-with-sql-database-managed-instance.md)。

## <a name="versions"></a>版本  

本地 SQL Server 發行者和分發伺服器必須使用以下版本之一(至少):  

- SQL 伺服器 2016 及更高
- SQL 伺服器 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014)或[SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL 伺服器 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2)或[SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> 嘗試使用不支援的版本配置複製可能會導致錯誤編號MSSQL_REPL20084(進程無法連接到訂閱伺服器)和MSSQL_REPL40532(無法打開>登錄請求的伺服器\<名稱。 登入失敗。)。  

若要使用 Azure SQL Database 的所有功能，您必須使用最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。  

  
## <a name="remarks"></a>備註

- 若要設定複寫，請使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或在發行者上執行 Transact-SQL 陳述式。 您無法使用 Azure 入口網站來設定複寫。  
- 複寫只能使用 SQL Server 驗證登入來連線到 Azure SQL 資料庫。
- 複寫的資料表都必須有主索引鍵。  
- 您必須具有現有的 Azure 訂用帳戶。  
- Azure SQL 資料庫訂閱者可以位在任何區域中。  
- SQL Server 上的單一發行集可同時支援 Azure SQL Database 和 SQL Server (內部部署和 Azure 虛擬機器中的 SQL Server) 訂閱者。  
- 複寫管理、監視和疑難排解都必須從內部部署 SQL Server 執行。  
- 僅支援對 Azure SQL Database 的發送訂閱。  
- 在 SQL Database 的 **sp_addsubscription** 中僅支援 `@subscriber_type = 0`。  
- Azure SQL Database 不支援雙向、即時、可更新或點對點複寫。

## <a name="replication-architecture"></a>複寫架構  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>案例  

### <a name="typical-replication-scenario"></a>一般複寫案例  

1. 在內部部署 SQL Server 資料庫上建立異動複寫發行集。  
2. 在內部部署 SQL Server 上使用 [新增訂閱精靈]**** 或 Transact-SQL 陳述式，來建立對 Azure SQL Database 的發送訂閱。  
3. 使用 Azure SQL Database 中的單一和集區資料庫，初始資料集是由快照集代理程式建立且由散發代理程式散發並套用的快照集。 使用受控執行個體資料庫時，也可以使用資料庫備份來植入訂閱者資料庫。

### <a name="data-migration-scenario"></a>資料移轉案例  

1. 若要將資料從內部部署 SQL Server 資料庫複寫到 Azure SQL Database 中，請使用異動複寫。  
2. 重新導向用戶端或中介層應用程式來更新 Azure SQL 資料庫複本。  
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
- 複製權限  

### <a name="limitations-to-be-determined"></a>待決定的限制

- 複製定序  
- SP 的序列式交易執行  

## <a name="examples"></a>範例

建立發行集和發送訂閱。 如需詳細資訊，請參閱
  
- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)，方法是使用 Azure SQL Database 伺服器名稱作為訂閱者 (例如 **N'azuresqldbdns.database.windows.net'**)，並使用 Azure SQL 資料庫名稱作為目的地資料庫 (例如 **AdventureWorks**)。  

## <a name="see-also"></a>另請參閱  

- [交易複製](sql-database-managed-instance-transactional-replication.md)
- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [複寫類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (複寫)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化訂閱](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
