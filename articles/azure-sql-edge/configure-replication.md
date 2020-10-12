---
title: 設定複寫至 Azure SQL Edge
description: 瞭解如何設定 Azure SQL Edge 的複寫。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a748bf977e76357c710518e608c12ad19a8cd0be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888428"
---
# <a name="configure-replication-to-azure-sql-edge"></a>設定複寫至 Azure SQL Edge 

您可以將 Azure SQL Edge 的實例設定為單向異動複寫或快照式複寫的發送訂閱者。 此實例無法作為異動複寫設定的發行者或散發者。 請注意，Azure SQL Edge 不支援合併式複寫、點對點複寫或 Oracle 發行。

## <a name="supported-configurations"></a>支援的設定
  
- Azure SQL Edge 的實例必須是發行者的發送訂閱者。
- 發行者和散發者可以是：
   - 在內部部署執行的 SQL Server 實例，或在 Azure 虛擬機器中執行的 SQL Server 實例。 如需詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](https://docs.microsoft.com/azure/azure-sql/virtual-machines/)。 SQL Server 實例必須使用比 SQL Server 2016 更晚的版本。
   - Azure SQL 受控執行個體的實例。 SQL 受控執行個體可以裝載發行者、散發者和訂閱者資料庫。 如需詳細資訊，請參閱[使用 SQL Database 受控執行個體進行複寫](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/)。

- 散發資料庫和複寫代理程式不能放在 Azure SQL Edge 的實例上。  

> [!NOTE]
> 如果您嘗試使用不支援的版本來設定複寫，您可能會收到下列兩個錯誤： MSSQL_REPL20084 ( 「此進程無法連接到訂閱者」。) 和 MSSQL_REPL40532 (」無法開啟 \<name> 登入所要求的伺服器。 登入失敗。」) 。  

## <a name="remarks"></a>備註

當您設定複寫時，必須瞭解下列需求和最佳作法：

- 您可以使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)來設定複寫。 您也可以在「發行者」上執行 Transact-sql 語句，方法是使用 SQL Server Management Studio 或 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)。
- 若要複寫至 Azure SQL Edge 的實例，您必須使用 SQL Server authentication 來登入。
- 複寫的資料表都必須有主索引鍵。
- SQL Server 上的單一發行集可同時支援 Azure SQL Edge 和 SQL Server (內部部署和 Azure 虛擬機器中的 SQL Server) 訂閱者。  
- 複寫管理、監視和疑難排解都必須從 SQL Server 實例執行。  
- 僅支援對 Azure SQL Edge 的發送訂閱。  
- 只有 `@subscriber_type = 0` AZURE SQL Edge 的預存程式支援 `sp_addsubscription` 。  
- Azure SQL Edge 不支援雙向、即時、可更新或點對點複寫。
- Azure SQL Edge 僅支援 SQL Server 或 SQL 受控執行個體提供的功能子集。 如果您嘗試複寫資料庫 (或包含一或多個不支援之功能的資料庫) 中的物件，則嘗試會失敗。 例如，如果您嘗試複寫的資料庫包含具有空間資料類型的物件，您將會收到錯誤訊息。 如需詳細資訊，請參閱 [AZURE SQL Edge 支援的功能](features.md)。

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>初始化 Azure SQL Edge 實例上的參考資料

您可能會想要使用隨時間變更的參考資料來初始化您的實例。 例如，您可能會想要在 Azure SQL Edge 實例上的機器學習模型經過定型之後，于 SQL Server 實例上進行更新。 以下是在這種情況下，如何初始化您的實例：

1. 在 SQL Server 資料庫上建立異動複寫發行集。  
2. 在 SQL Server 實例上，使用「 **新增訂閱嚮導** 」或 transact-sql 語句來建立 Azure SQL Edge 訂用帳戶的推播。  
3. 您可以使用快照集代理程式所產生的快照集，在 Azure SQL Edge 上初始化複寫的資料庫，並由散發代理程式散發和傳遞。 或者，您也可以從發行者使用資料庫的備份來初始化。 請記住，如果資料庫備份包含 Azure SQL Edge 不支援的物件或功能，則還原作業會失敗。

## <a name="limitations"></a>限制

Azure SQL Edge 訂閱不支援下列選項：

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
- 複製 filestream、 `hierarchyid` 或空間資料類型
- 轉換 `hierarchyid` 成 MAX 資料類型  
- 將 spatial 轉換為 MAX 資料類型  
- 複製擴充屬性  
- 複製權限  

## <a name="examples"></a>範例

建立發行集和發送訂閱。 如需詳細資訊，請參閱
  
- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 使用 Azure SQL Edge server 名稱和 IP 做為訂閱者來[建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) (例如， **myEdgeinstance、1433**) ，以及 azure SQL Edge 實例上的資料庫名稱做為目的地資料庫 (例如**AdventureWorks**) 。  

## <a name="next-steps"></a>後續步驟  

- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [複寫的類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (複寫) ](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化訂用帳戶](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


