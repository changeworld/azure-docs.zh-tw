---
title: Azure Arc 啟用的 SQL 受控執行個體的特性和功能
description: Azure Arc 啟用的 SQL 受控執行個體的特性和功能
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 810a08d2f72359b385d2a7567b796aa222c6ab14
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936795"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 啟用的 SQL 受控執行個體的特性和功能

Azure Arc 啟用的 SQL 受控執行個體與 SQL Server 的最新穩定版本共用通用的程式碼基底。 大部分的標準 SQL 語言、查詢處理和資料庫管理功能都相同。 SQL Server 與 SQL Database 或 SQL 受控執行個體之間常見的功能如下：

- 語言功能-[控制流程語言關鍵字](/sql/t-sql/language-elements/control-of-flow)、資料[指標](/sql/t-sql/language-elements/cursors-transact-sql)、[資料類型](/sql/t-sql/data-types/data-types-transact-sql)、 [DML 語句](/sql/t-sql/queries/queries)、 [Predicates](/sql/t-sql/queries/predicates)述詞、[序號](/sql/relational-databases/sequence-numbers/sequence-numbers)、[預存程式](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)和[變數](/sql/t-sql/language-elements/variables-transact-sql)。
- 資料庫功能- [自動調整 (計畫強制) ](/sql/relational-databases/automatic-tuning/automatic-tuning)、 [變更追蹤](/sql/relational-databases/track-changes/about-change-tracking-sql-server)、 [資料庫定序](/sql/relational-databases/collations/set-or-change-the-database-collation)、自主 [資料庫](/sql/relational-databases/databases/contained-databases)、 [包含的使用者](/sql/relational-databases/security/contained-database-users-making-your-database-portable)、 [資料壓縮](/sql/relational-databases/data-compression/data-compression)、 [資料庫設定](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)、 [線上索引作業](/sql/relational-databases/indexes/perform-index-operations-online)、資料 [分割](/sql/relational-databases/partitions/partitioned-tables-and-indexes)和 [時態表](/sql/relational-databases/tables/temporal-tables) ([請參閱快速入門手冊](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)) 。
- 安全性功能-[應用程式角色](/sql/relational-databases/security/authentication-access/application-roles)、[動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking) ([開始使用 Azure 入口網站) 的 SQL Database 動態資料遮罩](../../azure-sql/database/dynamic-data-masking-configure-portal.md)[資料列層級安全性](/sql/relational-databases/security/row-level-security)
- 多模型功能- [圖形處理](/sql/relational-databases/graphs/sql-graph-overview)、 [JSON 資料](/sql/relational-databases/json/json-data-sql-server)、 [OPENXML](/sql/t-sql/functions/openxml-transact-sql)、 [空間](/sql/relational-databases/spatial/spatial-data-sql-server)、 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql)和 [XML 索引](/sql/t-sql/statements/create-xml-index-transact-sql)。



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 啟用的 SQL 受控執行個體功能

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS 高可用性  
  
|功能|Azure Arc 啟用的 SQL 受控執行個體|
|-------------|----------------|
|記錄傳送|是| 
|備份壓縮|是|
|資料庫快照集|是|
|Always On 容錯移轉叢集執行個體<sup>1</sup>| 不適用。 可用的類似功能 |
|Always On 可用性群組<sup>2</sup>|已規劃 HA 功能。|
|基本可用性群組 <sup>2</sup>|已規劃 HA 功能。|
|最小複本認可哥用性群組 <sup>2</sup>|已規劃 HA 功能。|
|無叢集的可用性群組|是|
|線上頁面和檔案還原|是|
|線上檢索索引|是|
|繼續線上索引重建|是|
|線上結構描述變更|是|
|快速復原|是|
|鏡像備份|是|
|熱新增記憶體和 CPU|是|
|加密的備份|是|
|混合式備份至 Azure (備份至 URL)|是|

<sup>1</sup> 在發生 pod 失敗的案例中，新的 SQL 受控執行個體將會啟動，並重新附加至包含您資料的持續性磁片區。 [在此深入瞭解如何 Kubernetes 永久性磁片](https://kubernetes.io/docs/concepts/storage/persistent-volumes)區。

<sup>2</sup> 個未來版本將提供 AG 功能 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS 的擴充性和效能  

|功能|Azure Arc 啟用的 SQL 受控執行個體|
|-------------|----------------|
|columnstore|   是|
|叢集資料行存放區索引中的大型物件二進位檔|    是|
|線上非叢集資料行存放區索引重建| 是|
|記憶體內部 OLTP|    是|
|持續性的主記憶體|    是|
|資料表和索引分割區|  是
|資料壓縮|  是|
|資源管理員| 是|
|分割資料表平行處理原則| 是|
|NUMA 感知大型分頁記憶體和緩衝區陣列配置|  是|
|IO 資源管理|    是|
|延遲持久性|    是|
|自動調整|  是|
|批次模式自適性聯結| 是|
|批次模式記憶體授與意見反應|  是|
|交錯執行多重陳述式資料表值函式|  是|
|大量插入增強功能   |是|

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS 安全性  
|功能|Azure Arc 啟用的 SQL 受控執行個體|
|-------------|----------------|
|資料列層級安全性|    是|
|Always Encrypted|  是|
|具有安全記憶體保護區的 Always Encrypted| No|
|動態資料遮罩|  是|
|基本稽核|    是|
|細部稽核| 是|
|透明資料庫加密|   是|
|使用者定義角色|    是|
|自主資料庫|   是|
|備份的加密|    是|

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS 管理能力  

|功能|Azure Arc 啟用的 SQL 受控執行個體|
|-------------|----------------|
|專用管理員連接|    是|
|PowerShell 指令碼支援|  是|
|資料層應用程式元件作業的支援 - 擷取、部署、升級、刪除| 是
|原則自動化 (依排程和變更檢查)   |是|
|效能資料收集器|    是|
|標準效能報告   |是|
|計畫指南和計畫指南的計畫凍結| 是|
|索引檢視表的直接查詢 (使用 NOEXPAND 提示)|   是|
|自動索引檢視表維護    |是|
|分散式分割區檢視| 是|
|平行索引作業    |是|
|查詢最佳化工具自動使用索引檢視表|  是|
|平行一致性檢查 |是|


### <a name="programmability"></a><a name="Programmability"></a> Programmability  

|功能|Azure Arc 啟用的 SQL 受控執行個體|
|-------------|----------------|
|JSON|  是 |       |
|查詢存放區    |是    |       
|Temporal|  是 |       
|原生 XML 支援|    是 |       
|XML 索引   |是    |       
|MERGE 與 UPSERT 功能|   是 |       
|日期和時間資料類型    |是    |       
|國際化支援|  是 |       
|全文檢索和語意搜尋 |    No      |
|查詢中的語言規格 |是        |   
|Service Broker (訊息)|    是     |   
|Transact-SQL 端點|    是 |       
|圖形| 是 |   
|機器學習服務| No  |   
|PolyBase| No   |


### <a name="tools"></a>工具

Azure Arc 啟用的 SQL 受控執行個體支援各種可協助您管理資料的資料工具。

| **工具** | Azure Arc 啟用的 SQL 受控執行個體|
| --- | --- | --- |
| Azure 入口網站 <sup>1</sup> | No |
| Azure CLI | No |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | 是 |
| Azure PowerShell | Yes |
| [BACPAC 檔案 (匯出)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Yes |
| [BACPAC 檔案 (匯入)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Yes |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Yes |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Yes |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Yes |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Yes |

<sup>1</sup> Azure 入口網站僅用來在預覽期間，以唯讀模式來查看已啟用 AZURE ARC 的 SQL 受控實例。


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> & 服務的不支援功能

Azure Arc 啟用的 SQL 受控執行個體不提供下列功能和服務。 這些功能的支援將隨著時間逐漸啟用。

| 區域 | 不支援的功能或服務 |
|-----|-----|
| **資料庫引擎** | 合併式複寫 |
| &nbsp; | Stretch DB |
| &nbsp; | 搭配協力廠商連線的分散式查詢 |
| &nbsp; | 連結的伺服器與 SQL Server 和 Azure SQL 產品以外的資料來源 |
| &nbsp; | 系統擴充預存程序 (XP_CMDSHELL 等) |
| &nbsp; | FileTable、FILESTREAM |
| &nbsp; | 已設定 EXTERNAL_ACCESS 或 UNSAFE 權限的 CLR 組件 |
| &nbsp; | 緩衝集區擴充 |
| **SQL Server Agent** |  子系統：CmdExec、PowerShell、佇列讀取器、SSIS、SSAS、SSRS |
| &nbsp; | 警示 |
| &nbsp; | 受控備份 |
| **高可用性** | 資料庫鏡像  |
| **安全性** | 可延伸金鑰管理 |
| &nbsp; | 適用於連結伺服器的 AD 驗證 | 
| &nbsp; | 適用於可用性群組 (AG) 的 AD 驗證 | 