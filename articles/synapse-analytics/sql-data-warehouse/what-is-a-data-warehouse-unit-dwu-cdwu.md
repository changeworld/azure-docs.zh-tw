---
title: Azure 突觸分析(以前的 SQL DW)中的數據倉儲單元 (DWU)
description: 關於選擇理想數量的數據倉庫單位 (DWU) 以優化價格和性能以及如何更改單位數量的建議。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 01a05755fc18a85a95e9c1bec1c470d37af656d1
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632241"
---
# <a name="data-warehouse-units-dwus"></a>資料倉儲單位

關於選擇理想數量的數據倉庫單位 (DWU) 以優化價格和性能以及如何更改單位數量的建議。

## <a name="what-are-data-warehouse-units"></a>什麼是資料倉儲單位

[Synapse SQL 池](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse)表示正在預配的分析資源的集合。 分析資源定義為 CPU、記憶體和 IO 的組合。 

這三個資源被捆綁到稱為數據倉庫單元 (DWU) 的計算規模單位中。 DWU 能以抽象而標準化的量值來呈現計算資源與效能。 

對服務等級的更改會更改系統可用的 DWU 數量,從而調整系統的性能和成本。

為了獲得更高的性能,可以增加數據倉庫單位的數量。 為了降低性能,請減少數據倉庫單位。 儲存體和計算成本會分別計費，因此，變更資料倉儲單位不會影響儲存體成本。

資料倉儲單位的效能:

- 標準 SQL 池查詢可以掃描大量行,然後執行複雜的聚合的速度。 這個作業是 I/O 和 CPU 密集型作業。
- SQL 池從 Azure 存儲 Blob 或 Azure 數據湖中引入數據的速度有多快。 這個作業是網路和 CPU 密集型作業。
- [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL 命令複製表的速度有多快。 這個作業牽涉到從儲存體讀取資料、跨應用裝置的節點散發資料，以及重新寫入至儲存體。 這個作業是 CPU、IO 和網路密集型作業。

增加 DWU：

- 以線性方式變更掃描、彙總和 CTAS 陳述式的系統效能
- 針對 PolyBase 載入作業增加讀取器和寫入器的數目
- 增加並行查詢和並行位置的數目上限。

## <a name="service-level-objective"></a>服務等級目標

服務等級目標 (SLO) 是確定 SQL 池的成本和性能級別的可伸縮性設置。 Gen2 SQL 池的服務級別以數據倉庫單元 (DWU)(例如 DW2000c)來衡量。

在 T-SQL 中,SERVICE_OBJECTIVE設置確定 SQL 池的服務級別。

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>容量限制

每部 SQL 伺服器 (例如 myserver.database.windows.net) 都有[資料庫交易單位 (DTU)](../../sql-database/sql-database-service-tiers-dtu.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 配額，允許有特定數目的資料倉儲單位。 如需詳細資訊，請參閱[工作負載管理容量限制](sql-data-warehouse-service-capacity-limits.md#workload-management)。

## <a name="how-many-data-warehouse-units-do-i-need"></a>我需要多少個資料倉儲單位

理想的資料倉儲單位數大部分取決於您的工作負載，以及您已載入系統的資料量。

為工作負載尋找最佳 DWU 的步驟：

1. 首先選取較小的 DWU。
2. 當您的測試資料載入系統時監視應用程式效能，觀察比較所選 DWU 數目與您觀察到的效能。
3. 針對定期的尖峰活動期間，識別任何其他需求。 可能需要頻繁縮放活動中顯示顯著高峰和低谷的工作負載。

SQL 池是一個橫向擴展系統,它可以提供大量的計算和查詢量大的數據。 

若要查看真正用以調整的功能 (尤其是在較大的 DWU 上)，建議您在進行調整以確定有足夠資料可提供給 CPU 時調整資料集。 針對調整測試，我們建議至少使用 1 TB。

> [!NOTE]
>
> 如果工作可以在計算節點之間分割，則查詢效能只會隨更多的平行處理增加。 如果您發現調整並未變更效能，則可能需要調整資料表設計和/或您的查詢。 如需查詢微調指引，請參閱[管理使用者查詢](cheat-sheet.md)。

## <a name="permissions"></a>權限

變更資料倉儲單位需要 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) 中所述的權限。

SQL DB 參與者和 SQL Server 參與者等 Azure 資源內建角色可以變更 DWU 設定。

## <a name="view-current-dwu-settings"></a>檢視目前的 DWU 設定

檢視目前的 DWU 設定：

1. 在 Visual Studio 中開啟 [SQL Server 物件總管]。
2. 連接到與邏輯 SQL Database 伺服器相關聯的 master 資料庫。
3. 從 sys.database_service_objectives 動態管理檢視中選取。 範例如下：

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>變更資料倉儲單位

### <a name="azure-portal"></a>Azure 入口網站

要變更 DWU:

1. 開啟 [Azure 入口網站](https://portal.azure.com)、開啟您的資料庫，然後按一下 [調整]****。

2. 在 [調整]**** 下方，將滑桿向左或右移動來變更 DWU 設定。

3. 按一下 [檔案]  。 確認訊息隨即出現。 按一下 [是]**** 以確認或 [否]**** 以取消。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

要更改 DWU,請使用[集-AzSql 資料庫](/powershell/module/az.sql/set-azsqldatabase)PowerShell cmdlet。 以下範例將託管在伺服器上的 MySQLDW 資料庫的服務層級目標設定為 DW1000c。

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

如需詳細資訊，請參閱 [SQL 資料倉儲的 PowerShell Cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

使用 T-SQL,您可以查看當前的 DWU 設置、更改設定並檢查進度。

若要變更 DWU︰

1. 連接到與您的邏輯 SQL Database 伺服器相關聯的 master 資料庫。
2. 使用 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL 陳述式。 下面的範例將資料庫 MySQLDW 的服務等級目標設置為 DW1000c。

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>REST API

若要變更 DWU，請使用[建立或更新資料庫](/rest/api/sql/databases/createorupdate) REST API。 下面的範例將託管在伺服器上的 MySQLServer 資料庫的 DW1000c 的服務層級目標設定為 DW1000c。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

如需更多 REST API 範例，請參閱 [SQL 資料倉儲的 REST API](sql-data-warehouse-manage-compute-rest-api.md)。

## <a name="check-status-of-dwu-changes"></a>檢查 DWU 變更的狀態

DWU 變更可能需要幾分鐘的時間才能完成。 如果正在進行自動調整，請考慮實作邏輯，以確保在繼續進行其他動作之前，已完成特定作業。

透過各種端點檢查資料庫狀態，可讓您正確實作自動化。 入口網站會提供資料庫目前的狀態，也會在作業完成時提供通知，但不允許以程式設計方式檢查狀態。

您無法使用 Azure 入口網站來檢查相應放大作業的資料庫狀態。

檢查 DWU 變更的狀態：

1. 連接到與您的邏輯 SQL Database 伺服器相關聯的 master 資料庫。

1. 提交下列查詢以檢查資料庫狀態。

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```

1. 提交下列查詢以檢查作業的狀態

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```

此 DMV 傳回有關 SQL 池上各種管理操作的資訊,例如操作和操作的狀態,這些操作IN_PROGRESS或"已完成"。

## <a name="the-scaling-workflow"></a>調整工作流程

啟動縮放操作時,系統首先終止所有打開的會話,回滾任何打開的事務以確保一致狀態。 針對調整規模作業，只有在這個交易回復完成後調整才會發生。  

- 對於放大操作,系統將分離所有計算節點,規定額外的計算節點,然後重新附加到存儲層。
- 對於縮減操作,系統將分離所有計算節點,然後僅將所需的節點重新附加到存儲層。

## <a name="next-steps"></a>後續步驟

若要深入了解管理效能，請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)和[記憶體和並行存取限制](memory-concurrency-limits.md)。
