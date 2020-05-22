---
title: 設定複寫至 Azure SQL Edge (預覽)
description: 了解如何設定複寫至 Azure SQL Edge (預覽)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593987"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>設定複寫至 Azure SQL Edge (預覽) 

Azure SQL Edge 執行個體可以設定為單向交易複寫或快照式複寫的發送訂閱者。 Azure SQL Edge 執行個體無法作為交易複寫組態的發行者或散發者。 Azure SQL Edge 不支援合併式複寫、P2P 複寫及 Oracle 發行。

## <a name="supported-configurations"></a>**支援的組態**：
  
- Azure SQL Edge 執行個體必須是發行者的發送訂閱者。
- 發行者和散發者可以是
   - 在內部部署執行的 SQL Server 執行個體，或是在 Azure 虛擬機器中執行的 SQL Server 執行個體。 如需詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)。 SQL Server 執行個體必須使用 SQL Server 2016 以上的版本。
   - Azure SQL Database 受控執行個體的執行個體。 受控執行個體可以裝載發行者、散發者和訂閱者資料庫。 如需詳細資訊，請參閱[使用 SQL Database 受控執行個體進行複寫](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/)。

- 散發資料庫和複寫代理程式不能置於 Azure SQL Edge 執行個體上。  

> [!NOTE]
> 嘗試使用不支援的設定複寫版本可能會導致錯誤號碼 MSSQL_REPL20084 (處理序無法連線到訂閱者。) 和 MSSQL_REPL40532 (無法開啟登入所要求的公開伺服器 \<名稱>。 登入失敗。)。  

若要使用 Azure SQL Edge 的所有功能，您必須使用最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。  

## <a name="remarks"></a>備註

- 若要設定複寫，請使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)，或使用 SQL Server Management Studio 或 [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio) 在發行者上執行 Transact-SQL 陳述式
- 複寫只能使用 SQL Server 驗證登入連線到 Azure SQL Edge 執行個體。
- 複寫的資料表都必須有主索引鍵。
- SQL Server 上的單一發行集可同時支援 Azure SQL Edge 和 SQL Server (內部部署和 Azure 虛擬機器中的 SQL Server) 訂閱者。  
- 複寫管理、監視和疑難排解都必須從內部部署 SQL Server 執行。  
- 僅支援對 Azure SQL Edge 的發送訂閱。  
- Azure SQL Edge 的 **sp_addsubscription** 中僅支援 `@subscriber_type = 0`。  
- Azure SQL Edge 不支援雙向、即時、可更新或點對點複寫。
- Azure SQL Edge 僅支援 SQL Server 或 Azure SQL Database 受控執行個體中可用的功能子集，因為這類嘗試複寫包含一或多個不支援功能的資料庫 (或資料庫內的物件) 的動作會導致失敗。 例如，嘗試複寫包含具有空間資料類型之物件的資料庫，將會導致錯誤。 如需 Azure SQL Edge 所支援功能的詳細資訊，請參閱 [Azure SQL Edge 支援的功能](features.md)。

## <a name="scenarios"></a>案例  

### <a name="initializing-reference-data-on-an-edge-instance"></a>初始化 Edge 執行個體上的參考資料

常見的案例是，當需要使用隨著時間變更的參考資料來初始化邊緣執行個體時，複寫十分有用。 例如，在已於內部部署 SQL Server 執行個體上定型後，更新 Edge 執行個體上的 ML 模型。

1. 在內部部署 SQL Server 資料庫上建立異動複寫發行集。  
2. 在內部部署 SQL Server 上使用**新增訂用帳戶精靈**或 Transact-SQL 陳述式，來建立對 Azure SQL Edge 的發送訂閱。  
3. 您可以使用快照集代理程式所產生的快照集來初始化 Azure SQL Edge 上複寫的資料庫，並由散發代理程式散發和傳遞，或使用發行者的資料庫備份。 同樣地，如果資料庫備份包含 Azure SQL Edge 不支援的物件/功能，還原作業將會失敗。

## <a name="limitations"></a>限制

Azure SQL Edge 訂用帳戶不支援下列選項：

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
- 複製 filestream、hierarchyid 或空間資料類型。
- 將 hierarchyid 轉換為 MAX 資料類型  
- 將 spatial 轉換為 MAX 資料類型  
- 複製擴充屬性  
- 複製權限  

## <a name="examples"></a>範例

建立發行集和發送訂閱。 如需詳細資訊，請參閱
  
- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)，方法是使用 Azure SQL Edge 伺服器名稱/IP 作為訂閱者 (例如 **myEdgeinstance,1433**)，並使用 Azure SQL Edge 執行個體作為目的地資料庫 (例如 **AdventureWorks**)。  

## <a name="see-also"></a>另請參閱  

- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [複寫的類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (複寫)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化訂閱](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


