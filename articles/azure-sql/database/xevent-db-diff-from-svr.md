---
title: 擴充事件
description: 描述 Azure SQL Database 中的擴充事件 (XEvents)，以及事件工作階段與 Microsoft SQL Server 中的事件工作階段有如何的些微不同。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: c8f73c0789cd0211deeb66af5c7300a81d7b1be0
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619809"
---
# <a name="extended-events-in-azure-sql-database"></a>Azure SQL Database 中的擴充事件 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Azure SQL Database 中的擴充事件功能集是 SQL Server 和 Azure SQL 受控執行個體上功能強大的功能子集。

*XEvents* 是非正式暱稱，有時在部落格或其他非正式位置用於「擴充事件」。

擴充事件的其他資訊可在下列位置取得：

- [快速入門：SQL Server 中的延伸事件](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [擴充事件](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>必要條件

本主題假設您已經有一些下列項目的知識：

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [擴充事件](/sql/relational-databases/extended-events/extended-events)

- 關於擴充事件的大部分檔都適用于 SQL Server、Azure SQL Database 和 Azure SQL 受控執行個體。

當選擇事件檔案做為 [目標](#AzureXEventsTargets)時，事先公開下列項目很有幫助：

- [Azure 儲存體服務](https://azure.microsoft.com/services/storage/)

- [使用 Azure 儲存體 Azure PowerShell](/powershell/module/az.storage/)

## <a name="code-samples"></a>程式碼範例

相關的主題提供兩個程式碼範例：

- [Azure SQL Database 中擴充事件的信號緩衝區目的程式代碼](xevent-code-ring-buffer.md)

  - 簡短的簡單 Transact-SQL 指令碼。
  - 我們在程式碼範例主題中強調，當您完成「信號緩衝區」目標的相關作業時，應該執行 alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` 陳述式來釋出其資源。 稍後您可以藉由 `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`，加入信號緩衝區的另一個執行個體。

- [Azure SQL Database 中擴充事件的事件檔案目的程式代碼](xevent-code-event-file.md)

  - 階段 1 是 PowerShell，建立 Azure 儲存體容器。
  - 階段 2 是 Transact-SQL，使用 Azure 儲存體容器。

## <a name="transact-sql-differences"></a>Transact-SQL 差異

- 當您在 SQL Server 上執行 [CREATE EVENT SESSION](/sql/t-sql/statements/create-event-session-transact-sql) 命令時，您使用 **ON SERVER** 子句。 但是 Azure SQL Database 您改為使用 **ON Database** 子句。
- **ON DATABASE** 子句也適用於 [ALTER EVENT SESSION](/sql/t-sql/statements/alter-event-session-transact-sql) 和 [DROP EVENT SESSION](/sql/t-sql/statements/drop-event-session-transact-sql) Transact-SQL 命令。

- 最佳做法是在您的 **CREATE EVENT SESSION** 或 **ALTER EVENT SESSION** 陳述式中包含 **STARTUP_STATE = ON** 的事件工作階段選項。
  - **= ON** 值支援在由於容錯移轉而進行邏輯資料庫的重新設定之後，自動重新啟動。

## <a name="new-catalog-views"></a>新目錄檢視

擴充事件功能受到多個 [目錄檢視](https://msdn.microsoft.com/library/ms174365.aspx)支援。 目錄檢視會告訴您目前資料庫中使用者建立事件工作階段的 *中繼資料或定義* 的相關資訊。 檢視不會傳回作用中事件工作階段的執行個體的相關資訊。

| 名稱<br/>目錄檢視的名稱 | 描述 |
|:--- |:--- |
| **sys.database_event_session_actions** |針對事件工作階段之每個事件的每個動作傳回資料列。 |
| **sys.database_event_session_events** |針對事件工作階段中的每個事件傳回資料列。 |
| **sys.database_event_session_fields** |針對已在事件和目標上明確設定的每個可自訂資料行傳回資料列。 |
| **sys.database_event_session_targets** |傳回事件工作階段中每一個事件目標的資料列。 |
| **sys.database_event_sessions** |針對資料庫中的每個事件會話，各傳回一個資料列。 |

在 Microsoft SQL Server 中，類似的目錄檢視具有包含 .server_\_** 而不是 .database\_** 的名稱。 名稱模式類似 **sys.server_event_%**。

## <a name="new-dynamic-management-views-dmvs"></a>新的動態管理檢視 [(DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database 具有支援擴充事件的 [動態管理檢視 (DMV)](https://msdn.microsoft.com/library/bb677293.aspx) 。 DMV 會告訴您 *作用中* 事件工作階段的相關資訊。

| DMV 的名稱 | 描述 |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |傳回有關事件工作階段動作的資訊。 |
| **sys.dm_xe_database_session_events** |傳回有關工作階段事件的資訊。 |
| **sys.dm_xe_database_session_object_columns** |顯示繫結至工作階段之物件的組態值。 |
| **sys.dm_xe_database_session_targets** |會傳回工作階段目標的相關資訊。 |
| **sys.dm_xe_database_sessions** |針對範圍為目前資料庫的每個事件工作階段傳回資料列。 |

在 Microsoft SQL Server 中，類似的目錄檢視的命名方式沒有名稱的* \_ 資料庫*部分，例如：

- **sys.dm_xe_sessions**，而不是<br/>**sys.dm_xe_database_sessions**。

### <a name="dmvs-common-to-both"></a>兩者通用的 DMV

針對擴充的事件，Azure SQL Database、Azure SQL 受控執行個體和 Microsoft SQL Server 都有共同的額外 Dmv：

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>尋找可用擴充事件、動作和目標

您可以執行簡單 SQL **SELECT** 以取得可用事件、動作和目標的清單。

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>您 Azure SQL Database 事件會話的目標

以下是可在 Azure SQL Database 上從您的事件會話中捕捉結果的目標：

- [信號緩衝區目標](https://msdn.microsoft.com/library/ff878182.aspx) -在記憶體中簡短保留事件資料。
- [事件計數器目標](https://msdn.microsoft.com/library/ff878025.aspx) -會計算在擴充事件工作階段期間發生的所有事件。
- [事件檔案目標](https://msdn.microsoft.com/library/ff878115.aspx) - 會將完整緩衝區寫入 Azure 儲存體容器。

[Windows 事件追蹤 (ETW) ](https://msdn.microsoft.com/library/ms751538.aspx) API 無法用於 Azure SQL Database 上的擴充事件。

## <a name="restrictions"></a>限制

有幾個與安全性相關的差異這 Azure SQL Database 的雲端環境：

- 擴充事件是建構在單一租用戶隔離模型的基礎上。 一個資料庫中的事件工作階段無法從另一個資料庫存取資料或事件。
- 無法在 **master** 資料庫的內容中發出 **CREATE EVENT SESSION** 陳述式。

## <a name="permission-model"></a>權限模型

您必須擁有資料庫的**控制**權限，才能發出 **CREATE EVENT SESSION** 陳述式。 資料庫擁有者 (dbo) 有 **控制** 權限。

### <a name="storage-container-authorizations"></a>儲存體容器授權

您針對 Azure 儲存體容器產生的 SAS 權杖必須指定權限的 **rwl** 。 **rwl** 值會提供下列權限：

- 讀取
- 寫入
- 清單

## <a name="performance-considerations"></a>效能考量

有大量使用擴充事件會累積超出整體系統狀況良好所允許的更多作用中記憶體的案例。 因此 Azure SQL Database 動態設定和調整事件會話可以累積的使用中記憶體數量限制。 許多因素會列入動態計算。

如果您收到錯誤訊息，指出已強制執行記憶體最大值，您可以採取的一些更正動作為：

- 執行較少的並行事件工作階段。
- 透過您對於事件工作階段的 **CREATE** 和 **ALTER** 陳述式，減少您在 **MAX\_MEMORY** 子句中指定的記憶體數量。

### <a name="network-latency"></a>網路延遲

**事件檔案** 目標在將資料保存到 Azure 儲存體 Blob 時可能會遇到網路延遲或失敗。 Azure SQL Database 中的其他事件可能會在等候網路通訊完成時延遲。 此延遲會降低您的工作負載。

- 若要減輕這個效能風險，請避免在您的事件工作階段定義中將 **EVENT_RETENTION_MODE** 選項設為 **NO_EVENT_LOSS**。

## <a name="related-links"></a>相關連結

- 搭配[使用 Azure PowerShell 與 Azure 儲存體](/powershell/module/az.storage/)。
- [Azure 儲存體 Cmdlet](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [搭配使用 Azure PowerShell 與 Azure 儲存體](/powershell/module/az.storage/)
- [如何使用 .NET 的 Blob 儲存體](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [關於 Microsoft SQL Server 中擴充事件的 Jonathan Kehayias 部落格文章](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- Azure *服務更新*網頁，已透過參數將範圍縮小為 Azure SQL Database：
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
