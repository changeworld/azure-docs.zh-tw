---
title: 讀取複本上的查詢
description: Azure SQL 可讓您使用唯讀複本的容量來讀取工作負載，稱為讀取相應放大。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: bd393a897052dd0bd49851eee424c99ad1fcfb1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319420"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>使用唯讀複本來卸載唯讀查詢工作負載
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

在 [高可用性架構](high-availability-sla.md#premium-and-business-critical-service-tier-availability)中，每個單一資料庫、彈性集區資料庫，以及 Premium 和業務關鍵服務層級中的受控實例，都會自動布建主要讀寫複本和數個次要唯讀複本。 次要複本會以與主要複本相同的計算大小進行布建。 *讀取*相應放大功能可讓您使用其中一個唯讀複本的計算容量來卸載唯讀工作負載，而不是在讀寫複本上執行。 如此一來，某些唯讀工作負載可以與讀寫工作負載隔離，而且不會影響其效能。 這項功能適用于包含邏輯上分隔唯讀工作負載（例如分析）的應用程式。 在 Premium 和業務關鍵服務層級中，應用程式可以使用此額外容量獲得效能優勢，而不需額外費用。

當至少有一個次要複本建立時，超大規模服務層級也會提供 *讀取* 相應放大功能。 多個次要複本可用於負載平衡唯讀工作負載，而這些工作負載需要的資源比一個次要複本上提供的多。

基本、標準和一般用途服務層級的高可用性架構不包含任何複本。 這些服務層級中無法使用 *讀取* 相應放大功能。

下圖說明這項功能。

![唯讀複本](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

在新的 Premium、Business Critical 和超大規模資料庫上，預設會啟用 *讀取* 相應放大功能。 針對超大規模，預設會為新的資料庫建立一個次要複本。 

> [!NOTE]
> 受控執行個體的商務關鍵服務層級中，一律會啟用讀取相應放大。

如果您的 SQL 連接字串是以設定 `ApplicationIntent=ReadOnly` ，則會將應用程式重新導向至該資料庫或受控實例的唯讀複本。 如需如何使用屬性的詳細資訊 `ApplicationIntent` ，請參閱 [指定應用程式意圖](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果您想要確保應用程式連接至主要複本，而不論 `ApplicationIntent` SQL 連接字串中的設定為何，您必須在建立資料庫時或變更其設定時明確停用讀取相應放大。 例如，如果您將資料庫從標準或一般用途層升級為 Premium、業務關鍵或超大規模層，而且想要確定所有連線都繼續移至主要複本，請停用讀取相應放大。如需有關如何停用的詳細資訊，請參閱 [啟用和停用讀取相應](#enable-and-disable-read-scale-out)放大。

> [!NOTE]
> 唯讀複本上不支援查詢存放區和 SQL Profiler 功能。 

## <a name="data-consistency"></a>資料一致性

複本的其中一個優點是複本一律處於交易一致狀態，但在不同的時間點，不同的複本之間可能會有些微延遲。 讀取相應放大支援工作階段層級的一致性。 這表示，如果唯讀會話在100無法使用複本所造成的連線錯誤之後重新連接，則可能會被重新導向至非最新的複本（具有讀寫複本）。 同樣地，如果應用程式使用讀寫工作階段來寫入資料，且使用唯讀工作階段來立即讀取該資料，則可能不會在複本上立即看到最新的更新。 此延遲是由非同步交易記錄重做作業所造成。

> [!NOTE]
> 區域內的複寫延遲很低，而這種情況很罕見。 若要監視複寫延遲，請參閱 [監視和疑難排解唯讀複本](#monitoring-and-troubleshooting-read-only-replicas)。

## <a name="connect-to-a-read-only-replica"></a>連線至唯讀複本

當您為資料庫啟用讀取相應放大時， `ApplicationIntent` 用戶端所提供之連接字串中的選項會指定連接是否要路由傳送至寫入複本或唯讀複本。 具體而言，如果 `ApplicationIntent` 值 `ReadWrite` (預設值) ，連接將會導向至讀寫複本。 這與 `ApplicationIntent` 不包含在連接字串中的行為相同。 如果 `ApplicationIntent` 值為 `ReadOnly`，則連線會路由傳送至唯讀的複本。

例如，下列連接字串會將用戶端連線至唯讀複本 (請將角括號中的項目取代為您的環境適用的值，並去除角括號)：

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

下列連接字串會將用戶端連線至讀寫複本 (請將角括號中的項目取代為您的環境適用的值，並去除角括號)：

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>確認已連線到唯讀複本

您可以在資料庫的內容中執行下列查詢，以確認您是否已連線到唯讀複本。 當您連接到唯讀複本時，它會傳回 READ_ONLY。

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> 在 Premium 和業務關鍵服務層級中，在任何指定時間都只能存取其中一個唯讀複本。 超大規模支援多個唯讀複本。

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>監視和疑難排解唯讀複本

當連線到唯讀複本時，動態管理檢視 (Dmv) 反映複本的狀態，而且可以查詢以進行監視和疑難排解用途。 資料庫引擎提供多個視圖來公開各種不同的監視資料。 

常用的視圖如下：

| 名稱 | 目的 |
|:---|:---|
|[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| 提供過去一小時的資源使用率計量，包括 CPU、資料 IO 和記錄寫入使用量（相對於服務目標限制）。|
|[sys.dm_os_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| 提供 database engine 實例的匯總等候統計資料。 |
|[sys.dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| 提供複本健全狀況狀態和同步處理統計資料。 重做佇列大小與重做速率可作為唯讀複本上的資料延遲指標。 |
|[sys.dm_os_performance_counters](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| 提供 database engine 效能計數器。|
|[sys.dm_exec_query_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| 提供依查詢執行的統計資料，例如執行次數、使用的 CPU 時間等等。|
|[sys.dm_exec_query_plan ( # B1 ](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| 提供快取的查詢計劃。 |
|[sys.dm_exec_sql_text ( # B1 ](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| 提供快取查詢計劃的查詢文字。|
|[sys.dm_exec_query_profiles](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 提供查詢執行時的即時查詢進度。|
|[sys.dm_exec_query_plan_stats ( # B1 ](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 提供最後一個已知的實際執行計畫，包括查詢的執行時間統計資料。|
|[sys.dm_io_virtual_file_stats ( # B1 ](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| 提供所有資料庫檔案的儲存體 IOPS、輸送量和延遲統計資料。 |

> [!NOTE]
> `sys.resource_stats` `sys.elastic_pool_resource_stats` 邏輯 master 資料庫中的和 dmv 會傳回主要複本的資源使用量資料。

### <a name="monitoring-read-only-replicas-with-extended-events"></a>使用擴充事件監視唯讀複本

連接到唯讀複本時，無法建立擴充的事件會話。 不過，在 Azure SQL Database 中，在主要複本上建立並改變的資料庫範圍 [擴充事件](xevent-db-diff-from-svr.md) 會話的定義，會複寫至唯讀複本，包括異地複本，以及在唯讀複本上捕獲事件。

以主要複本的會話定義為基礎之唯讀複本上的擴充事件會話，可以獨立于主要複本之外啟動及停止。 在主要複本上卸載擴充的事件會話時，它也會在所有唯讀複本上卸載。

### <a name="transaction-isolation-level-on-read-only-replicas"></a>唯讀複本上的交易隔離等級

在唯讀複本上執行的查詢一律會對應到 [快照](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) 集交易隔離等級。 快照隔離會使用資料列版本設定，以避免封鎖讀取器封鎖寫入器的情況。

在罕見的情況下，如果快照隔離交易存取已在另一個並行交易中修改過的物件中繼資料，可能會收到錯誤 [3961](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error)：「資料庫 '%. * ls ' 中的快照集隔離交易失敗，因為這個交易啟動之後，另一個並行交易中的 DDL 語句修改了此語句所存取的物件。 因為中繼資料並未建立版本，因此不允許此情況發生。 如果與快照集隔離混合，中繼資料的並行更新可能會造成不一致的情況。」

### <a name="long-running-queries-on-read-only-replicas"></a>唯讀複本上長時間執行的查詢

在唯讀複本上執行的查詢需要存取查詢 (資料表中所參考物件的中繼資料，索引、統計資料等等 ) 在罕見的情況下，如果在主要複本上修改中繼資料物件，而查詢在唯讀複本上持有相同物件的鎖定，則查詢會 [封鎖](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) 將主要複本的變更套用至唯讀複本的進程。 如果這類查詢很長一段時間執行，就會導致唯讀複本與主要複本的同步處理明顯不同步。 

如果唯讀複本上長時間執行的查詢導致這種封鎖，則會自動終止，而且會話將會收到錯誤1219：「您的會話因為高優先順序的 DDL 作業而中斷連線」。

> [!NOTE]
> 如果您在對唯讀複本執行查詢時收到錯誤3961或錯誤1219，請重試查詢。

> [!TIP]
> 在 Premium 和業務關鍵服務層級中，當連接到唯讀複本時， `redo_queue_size` `redo_rate` [sys.dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV 中的和資料行可用來監視資料同步處理常式，做為唯讀複本上的資料延遲指標。
> 

## <a name="enable-and-disable-read-scale-out"></a>啟用和停用讀取相應放大

依預設，會在 Premium、業務關鍵和超大規模服務層級上啟用讀取相應放大。 無法在基本、標準或一般用途服務層級中啟用讀取相應放大。 在設定為零個複本的超大規模資料庫上，會自動停用讀取相應放大。

您可以使用下列方法，在 Premium 或業務關鍵服務層級中的單一資料庫和彈性集區資料庫上停用並重新啟用讀取相應放大。

> [!NOTE]
> 針對單一資料庫和彈性集區資料庫，可停用讀取相應放大功能，以提供回溯相容性。 無法在商務關鍵受控實例上停用讀取相應放大。

### <a name="azure-portal"></a>Azure 入口網站

您可以在 [ **設定** 資料庫] 分頁上管理讀取相應放大設定。

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> 仍支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 Azure Resource Manager 模組將持續收到錯誤修正，直到2020年12月為止。  Az 模組和 Azure Resource Manager 模組中命令的引數本質上相同。 如需相容性的詳細資訊，請參閱 [新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

在 Azure PowerShell 中管理讀取相應放大需要2016年12月 Azure PowerShell 發行或更新版本。 如需最新 PowerShell 版本的相關資訊，請參閱 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

您可以藉由叫用 [>set-azsqldatabase](/powershell/module/az.sql/set-azsqldatabase) Cmdlet，並傳入所需的值 (`Enabled` 或 `Disabled`) 參數，在 Azure PowerShell 中停用或重新啟用讀取相應放大 `-ReadScale` 。

若要在現有的資料庫上停用讀取相應放大 (請將角括弧中的專案取代為您環境的正確值，並卸載角括弧) ：

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

若要在新的資料庫上停用讀取相應放大 (請將角括弧中的專案取代為您環境的正確值，並卸載角括弧) ：

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

若要在現有的資料庫上重新啟用讀取相應放大 (請將角括弧中的專案取代為您環境的正確值，並卸載角括弧) ：

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

若要建立已停用讀取相應放大的資料庫，或變更現有資料庫的設定，請使用下列方法，並將 `readScale` 屬性設定為 `Enabled` 或 `Disabled` ，如下列範例要求所示。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

如需詳細資訊，請參閱 [資料庫-建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>使用 `tempdb` 唯讀複本上的資料庫

`tempdb`主要複本上的資料庫不會複寫至唯讀複本。 每個複本都有自己的 `tempdb` 資料庫，此資料庫會在建立複本時建立。 這可確保 `tempdb` 可更新，並可在查詢執行期間進行修改。 如果您的唯讀工作負載相依于使用 `tempdb` 物件，您應該將這些物件建立為查詢腳本的一部分。

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>搭配異地複寫資料庫使用讀取縮放

異地複寫的次要資料庫具有與主資料庫相同的高可用性架構。 如果您要連線到已啟用讀取相應放大的異地複寫次要資料庫，則您的會話 `ApplicationIntent=ReadOnly` 將會路由傳送至其中一個高可用性複本，就像是在主要可寫入的資料庫上路由傳送一樣。 未設定 `ApplicationIntent=ReadOnly` 的工作階段會路由傳送至異地複寫次要的主要複本，這也是唯讀狀態。 

以這種方式，建立異地複寫會為讀寫主資料庫提供兩個以上的唯讀複本，總共有三個唯讀複本。 每個額外的地理複本都會提供另一對唯讀複本。 您可以在任何 Azure 區域中建立異地複本，包括主資料庫的區域。

> [!NOTE]
> 異地複寫次要資料庫的複本之間沒有自動迴圈配置資源或任何其他負載平衡路由。

## <a name="next-steps"></a>後續步驟

- 如需 SQL Database 超大規模供應專案的詳細資訊，請參閱 [超大規模服務層級](service-tier-hyperscale.md)。
