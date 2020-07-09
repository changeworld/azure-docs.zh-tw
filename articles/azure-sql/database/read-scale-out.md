---
title: 讀取複本上的查詢
description: Azure SQL 可讓您針對讀取工作負載（稱為讀取相應放大）使用唯讀複本的容量。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/26/2020
ms.openlocfilehash: cf9f48b0907d3bfe1d07dcffcc0d0b9534f74c83
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135900"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>使用唯讀複本來卸載唯讀查詢工作負載
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

作為[高可用性架構](high-availability-sla.md#premium-and-business-critical-service-tier-availability)的一部分，Premium 和商務關鍵服務層級中的每個資料庫和受控實例都會自動布建主要讀寫複本和數個次要唯讀複本。 次要複本會以與主要複本相同的計算大小進行布建。 *讀取相應放大*功能可讓您使用其中一個唯讀複本的計算容量來卸載唯讀工作負載，而不是在讀寫複本上執行它們。 如此一來，某些唯讀工作負載可以與讀寫工作負載隔離，而且不會影響其效能。 此功能適用于包含邏輯上分隔唯讀工作負載（例如分析）的應用程式。 在 Premium 和商務關鍵性服務層級中，應用程式可以使用此額外容量來獲得效能優勢，而不需額外成本。

當至少建立一個次要複本時，超大規模資料庫服務層級也會提供*讀取*相應放大功能。 有多個次要複本可用於負載平衡唯讀工作負載，而該工作量需要的資源比一個次要複本上的可用性更多。

「基本」、「標準」和「一般用途」服務層級的高可用性架構不包含任何複本。 *讀取相應放大*功能在這些服務層級中無法使用。

下圖說明此功能。

![唯讀複本](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

在新的 Premium、業務關鍵和超大規模資料庫資料庫上，預設會啟用*讀取相應放大*功能。 針對超大規模資料庫，預設會為新的資料庫建立一個次要複本。 

> [!NOTE]
> 在受控執行個體的商務關鍵服務層級中，一律會啟用讀取相應放大。

如果您的 SQL 連接字串是以設定 `ApplicationIntent=ReadOnly` ，應用程式將會被重新導向至該資料庫或受控實例的唯讀複本。 如需如何使用屬性的詳細資訊 `ApplicationIntent` ，請參閱[指定應用程式意圖](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果您想要確保應用程式連接到主要複本，而不論 `ApplicationIntent` SQL 連接字串中的設定為何，您必須在建立資料庫時或在改變其設定時，明確停用讀取相應放大。 例如，如果您將資料庫從標準或一般用途層升級至高階、業務關鍵或超大規模資料庫層，而且想要確定所有連線都會繼續移至主要複本，請停用讀取相應放大。如需如何停用它的詳細資訊，請參閱[啟用和停用讀取相應放大](#enable-and-disable-read-scale-out)。

> [!NOTE]
> 唯讀複本上不支援查詢存放區和 SQL Profiler 功能。 

## <a name="data-consistency"></a>資料一致性

複本的其中一個優點是複本一律處於交易一致狀態，但在不同的時間點，不同的複本之間可能會有些微延遲。 讀取相應放大支援工作階段層級一致性。 這表示，如果唯讀會話在無法使用複本所造成的連線錯誤之後重新連線，它可能會被重新導向至與讀寫複本不是100% 最新的複本。 同樣地，如果應用程式使用讀寫會話寫入資料，並且使用唯讀會話立即讀取，則可能不會立即在複本上看到最新的更新。 延遲是因非同步交易記錄重做作業所造成。

> [!NOTE]
> 區域內的複寫延遲很低，而這種情況很罕見。 若要監視複寫延遲，請參閱[監視和疑難排解唯讀複本](#monitoring-and-troubleshooting-read-only-replicas)。

## <a name="connect-to-a-read-only-replica"></a>連線至唯讀複本

當您啟用資料庫的讀取相應放大時， `ApplicationIntent` 用戶端所提供之連接字串中的選項會指出連接是否會路由傳送至寫入複本或唯讀複本。 具體而言，如果 `ApplicationIntent` 值為 `ReadWrite` （預設值），連接將會導向至讀寫複本。 這與 `ApplicationIntent` 不包含在連接字串中的行為相同。 如果 `ApplicationIntent` 值為 `ReadOnly`，則連線會路由傳送至唯讀的複本。

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

您可以在資料庫的內容中執行下列查詢，以確認您是否已連接至唯讀複本。 當您連接到唯讀複本時，它會傳回 READ_ONLY。

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> 在 Premium 和商務關鍵服務層級中，只有一個唯讀複本可在任何指定時間存取。 超大規模資料庫支援多個唯讀複本。

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>監視和疑難排解唯讀複本

當連接到唯讀複本時，動態管理檢視（Dmv）會反映複本的狀態，而且可以查詢以進行監視和疑難排解。 資料庫引擎會提供多個視圖來公開各種不同的監視資料。 

常用的觀點包括：

| 名稱 | 目的 |
|:---|:---|
|[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| 提供過去一小時的資源使用率計量，包括 CPU、資料 IO 和記錄寫入使用率（相對於服務目標限制）。|
|[sys.dm_os_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| 提供資料庫引擎實例的匯總等候統計資料。 |
|[sys. dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| 提供複本健全狀況狀態和同步處理統計資料。 重做佇列大小和重做速率會作為唯讀複本上的資料延遲指標。 |
|[sys.dm_os_performance_counters](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| 提供資料庫引擎效能計數器。|
|[sys.dm_exec_query_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| 提供每個查詢執行的統計資料，例如次數、使用的 CPU 時間等等。|
|[sys. dm_exec_query_plan （）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| 提供快取的查詢計劃。 |
|[sys. dm_exec_sql_text （）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| 提供快取查詢計劃的查詢文字。|
|[sys.dm_exec_query_profiles](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 提供查詢執行時的即時查詢進度。|
|[sys. dm_exec_query_plan_stats （）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 提供最後已知的實際執行計畫，包括查詢的執行時間統計資料。|
|[sys. dm_io_virtual_file_stats （）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| 提供所有資料庫檔案的儲存體 IOPS、輸送量和延遲統計資料。 |

> [!NOTE]
> `sys.resource_stats` `sys.elastic_pool_resource_stats` 邏輯 master 資料庫中的和 dmv 會傳回主要複本的資源使用量資料。

### <a name="monitoring-read-only-replicas-with-extended-events"></a>監視具有擴充事件的唯讀複本

當連接到唯讀複本時，無法建立擴充的事件會話。 不過，在 Azure SQL Database 中，在主要複本上建立和變更的資料庫範圍[擴充事件](xevent-db-diff-from-svr.md)會話的定義會複寫到唯讀複本（包括異地複本），並在唯讀複本上捕捉事件。

以主要複本的會話定義為基礎之唯讀複本上的擴充事件會話，可以獨立于主要複本啟動和停止。 中斷主要複本上的擴充事件會話時，它也會在所有唯讀複本上卸載。

### <a name="transaction-isolation-level-on-read-only-replicas"></a>唯讀複本上的交易隔離等級

在唯讀複本上執行的查詢一律會對應到[快照](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server)集交易隔離等級。 快照集隔離會使用資料列版本設定，以避免封鎖讀取器封鎖寫入器的案例。

在罕見的情況下，如果快照集隔離交易存取已在另一個並行交易中修改的物件中繼資料，則可能會收到錯誤[3961](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error)：「資料庫 '%. * ls ' 中的快照集隔離交易失敗，因為在此交易開始之後，另一個並行交易中的 DDL 語句已經修改了該語句存取的物件。 因為中繼資料並未建立版本，因此不允許此情況發生。 如果與快照集隔離混合，中繼資料的並行更新可能會導致不一致。」

### <a name="long-running-queries-on-read-only-replicas"></a>唯讀複本上的長時間執行查詢

在唯讀複本上執行的查詢需要存取查詢中所參考物件的中繼資料（資料表、索引、統計資料等）在罕見的情況下，如果在主要複本上修改中繼資料物件，而查詢在唯讀複本上持有相同物件的鎖定，則查詢會[封鎖](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK)將變更從主要複本套用到唯讀複本的進程。 如果這類查詢很長一段時間執行，就會導致唯讀複本與主要複本不同步。 

如果唯讀複本上的長時間執行查詢造成這種封鎖，則會自動終止，而且會話會收到錯誤1219：「因為高優先順序的 DDL 作業，您的會話已中斷連線」。

> [!NOTE]
> 如果您在對唯讀複本執行查詢時收到錯誤3961或錯誤1219，請重試查詢。

> [!TIP]
> 在高階和商務關鍵服務層級中，當連接到唯讀複本時， `redo_queue_size` `redo_rate` 會使用[dm_database_replica_states sys.databases](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)中的和資料行來監視資料同步處理常式，以做為唯讀複本上的資料延遲指示器。
> 

## <a name="enable-and-disable-read-scale-out"></a>啟用和停用讀取相應放大

在 Premium、業務關鍵和超大規模資料庫服務層級上，預設會啟用讀取相應放大。 在基本、標準或一般用途服務層級中，無法啟用讀取相應放大。 在設定了零個複本的超大規模資料庫資料庫上，會自動停用讀取相應放大。

您可以使用下列方法，在 Premium 或業務關鍵服務層級中的單一資料庫和彈性集區資料庫上，停用並重新啟用讀取相應放大。

> [!NOTE]
> 針對單一資料庫和彈性集區資料庫，會針對回溯相容性來停用讀取向外延展功能。 無法在商務關鍵性受控實例上停用讀取相應放大。

### <a name="azure-portal"></a>Azure 入口網站

您可以在 [**設定**資料庫] 分頁上管理讀取相應放大設定。

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager 模組仍受到支援，但所有未來的開發都是針對 Az .Sql 模組。 Azure Resource Manager 模組會繼續收到錯誤修正，直到2020年12月為止。  Az 模組和 Azure Resource Manager 模組中命令的引數本質上完全相同。 如需其相容性的詳細資訊，請參閱[新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

管理 Azure PowerShell 中的讀取相應放大需要2016年12月 Azure PowerShell 發行或更新版本。 如需最新 PowerShell 版本的相關資訊，請參閱 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

您可以叫用[set-azsqldatabase 搭配](/powershell/module/az.sql/set-azsqldatabase)指令程式，並傳入所需的參數值（或），以在 Azure PowerShell 中停用或重新啟用讀取相應放大 `Enabled` `Disabled` `-ReadScale` 。

停用現有資料庫的讀取相應放大（以您環境的正確值取代角括弧中的專案，並卸載角括弧）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

停用新資料庫的讀取相應放大（以您環境的正確值取代角括弧中的專案，並卸載角括弧）：

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

若要在現有的資料庫上重新啟用讀取相應放大（以您環境的正確值取代角括弧中的專案，並卸載角括弧）：

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

如需詳細資訊，請參閱[資料庫-建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>`tempdb`在唯讀複本上使用資料庫

`tempdb`主要複本上的資料庫不會複寫至唯讀複本。 每個複本都有自己 `tempdb` 的資料庫，它是在建立複本時所建立的。 這可確保 `tempdb` 是可更新的，而且可以在查詢執行期間修改。 如果您的唯讀工作負載相依于使用 `tempdb` 物件，您應該在查詢腳本中建立這些物件。

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>搭配異地複寫資料庫使用讀取相應放大

異地複寫的次要資料庫具有與主資料庫相同的高可用性架構。 如果您要連線到已啟用讀取相應放大的異地複寫次要資料庫，您的會話將會 `ApplicationIntent=ReadOnly` 路由傳送至其中一個高可用性複本，其方式與在主要可寫入資料庫上的路由相同。 未設定 `ApplicationIntent=ReadOnly` 的工作階段會路由傳送至異地複寫次要的主要複本，這也是唯讀狀態。 

如此一來，建立異地複本會為讀寫主資料庫提供兩個以上的唯讀複本，總共有三個唯讀複本。 每個額外的地理複本都會提供另一對唯讀複本。 可以在任何 Azure 區域中建立異地複本，包括主資料庫的區域。

> [!NOTE]
> 異地複寫之次要資料庫的複本之間沒有自動迴圈配置資源或任何其他負載平衡路由。

## <a name="next-steps"></a>後續步驟

- 如需 SQL Database 超大規模資料庫供應專案的相關資訊，請參閱[超大規模資料庫服務層級](service-tier-hyperscale.md)。
