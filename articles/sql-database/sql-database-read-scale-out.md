---
title: 讀取副本上的查詢
description: Azure SQL 資料庫提供使用唯讀副本（稱為讀取橫向擴展）的容量來負載平衡唯讀工作負載的功能。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206940"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>使用唯讀複本對唯讀查詢工作負載進行負載平衡

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

作為[高可用性體系結構](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)的一部分，高級和業務關鍵型服務層中的每個資料庫都自動預配為主副本和多個輔助副本。 輔助副本的預配具有與主副本相同的計算大小。 **讀取橫向擴展**功能允許您使用其中一個唯讀副本的容量來負載平衡 SQL 資料庫唯讀工作負載，而不是共用讀寫副本。 這種方式的唯讀工作負載將會與主要讀寫工作負載隔離，而且不會影響其效能。 此功能適用于包含邏輯分隔的唯讀工作負載（如分析）的應用程式。 在高級和業務關鍵型服務層中，應用程式可以使用此額外容量獲得性能優勢，無需額外成本。

在創建至少一個輔助副本時 **，"讀取橫向擴展"** 功能在超大規模服務層中也可用。 如果唯讀工作負荷需要的資源多於一個輔助副本上可用的資源，則可以使用多個輔助副本。 基本、標準和泛型服務層的高可用性體系結構不包含任何副本。 **讀取橫向擴展**功能在這些服務層中不可用。

下圖說明瞭它使用業務關鍵型資料庫。

![唯讀複本](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

預設情況下，在新的高級資料庫、業務關鍵型和超大規模資料庫上啟用"讀取橫向擴展"功能。 對於超大規模，預設情況下為新資料庫創建一個輔助副本。 如果 SQL 連接字串配置了`ApplicationIntent=ReadOnly`，則閘道將應用程式重定向到該資料庫的唯讀副本。 有關如何使用`ApplicationIntent`屬性的資訊，請參閱[指定應用程式意圖](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果要確保應用程式連接到主副本，而不考慮 SQL 連接字串中的`ApplicationIntent`設置，則必須在創建資料庫或更改其配置時顯式禁用讀取橫向擴展。 例如，如果將資料庫從標準層或通用層升級到高級、業務關鍵或超大規模層，並希望確保所有連接繼續轉到主副本，請禁用"讀取橫向擴展"。有關如何禁用它的詳細資訊，請參閱[啟用和禁用讀取橫向擴展](#enable-and-disable-read-scale-out)。

> [!NOTE]
> 唯讀副本不支援查詢資料存儲、擴展事件和 SQL 探測器功能。

## <a name="data-consistency"></a>資料一致性

複本的其中一個優點是複本一律處於交易一致狀態，但在不同的時間點，不同的複本之間可能會有些微延遲。 讀取相應放大支援工作階段層級一致性。 這意味著，如果唯讀會話在副本不可用導致的連接錯誤後重新連接，則可能會將其重定向到與讀寫副本不 100% 最新的副本。 同樣，如果應用程式使用讀寫會話寫入資料並立即使用唯讀會話讀取資料，則副本上可能無法立即看到最新的更新。 延遲由非同步事務日誌重做操作引起。

> [!NOTE]
> 區域內的複寫延遲較低，這種情況很罕見。

## <a name="connect-to-a-read-only-replica"></a>連線至唯讀複本

當您為資料庫啟用讀取相應放大時，用戶端所提供的連接字串中的 `ApplicationIntent` 選項會指出連線應路由至寫入複本還是唯讀複本。 具體來說，如果 `ApplicationIntent` 值為 `ReadWrite` (預設值)，則連線將會導向至資料庫的讀寫複本。 這與現有行為相同。 如果 `ApplicationIntent` 值為 `ReadOnly`，則連線會路由傳送至唯讀的複本。

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

您可以執行下列查詢，確認是否已連線到唯讀複本。 它會在連線到唯讀複本時傳回 READ_ONLY。

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> 在任何指定時間，ReadOnly 工作階段只能存取其中一個 AlwaysON 複本。

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>監視和故障排除唯讀副本

連接到唯讀副本時，可以使用`sys.dm_db_resource_stats`DMV 訪問性能指標。 要訪問查詢計劃統計資訊，請使用`sys.dm_exec_query_stats`和`sys.dm_exec_query_plan` `sys.dm_exec_sql_text` DMV。

> [!NOTE]
> 邏輯主資料庫`sys.resource_stats`中的 DMV 返回主副本的 CPU 使用率和存儲資料。

## <a name="enable-and-disable-read-scale-out"></a>啟用和停用讀取縮放

預設情況下，在高級、業務關鍵和超大規模服務層上啟用讀取橫向擴展。 無法在基本、標準或泛型服務層中啟用讀取橫向擴展。 在配置了 0 個副本的超大規模資料庫中自動禁用讀取橫向擴展。

您可以使用以下方法在高級或業務關鍵型服務層中的單個資料庫和彈性池資料庫中禁用和重新啟用讀取橫向擴展。

> [!NOTE]
> 為向後相容性提供了禁用讀取橫向擴展的功能。

### <a name="azure-portal"></a>Azure 入口網站

您可以在 **"配置**資料庫"邊欄選項卡上管理"讀取橫向擴展"設置。

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure 資源管理器 （RM） 模組仍受 Azure SQL 資料庫支援，但所有後續開發都針對 Az.Sql 模組。 AzureRM 模組將繼續接收錯誤修復，至少直到 2020 年 12 月。  Az 模組和 AzureRm 模組中命令的參數基本相同。 有關其相容性的更多資訊，請參閱[介紹新的 Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az)。

要在 Azure PowerShell 中管理讀取相應放大，必須使用 2016 年 12 月版的 Azure PowerShell 或更新版本。 如需最新 PowerShell 版本的相關資訊，請參閱 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

您可以通過調用[Set-AzSql資料庫](/powershell/module/az.sql/set-azsqldatabase)Cmdlet 並傳遞`Enabled``Disabled``-ReadScale`參數所需的值或 - 在 Azure PowerShell 中禁用或重新啟用讀取橫向擴展。

要禁用現有資料庫上的讀取橫向擴展（將角括弧中的項替換為環境的正確值並刪除角度括弧）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

要禁用新資料庫上的讀取橫向擴展（將角括弧中的項替換為環境的正確值並刪除角度括弧）：

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

要在現有資料庫上重新啟用讀取橫向擴展（將角括弧中的項替換為環境的正確值並刪除角度括弧）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

要創建禁用讀取橫向擴展的資料庫，或更改現有資料庫的設置，請使用以下方法將`readScale`屬性設置為`Enabled`或`Disabled`如以下示例請求中所示。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

如需詳細資訊，請參閱[資料庫 - 建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。

## <a name="using-tempdb-on-read-only-replica"></a>在唯讀副本上使用 TempDB

TempDB 資料庫不會複製到唯讀副本。 每個副本都有其自己的 TempDB 資料庫版本，該版本是在棄置站台時創建的。 它可確保 TempDB 是可更新的，可以在查詢執行期間進行修改。 如果唯讀工作負荷依賴于使用 TempDB 物件，則應將這些物件創建為查詢腳本的一部分。

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>對異地複寫的資料庫使用讀取縮放

如果使用"讀取橫向"來在異地複製的資料庫上（例如，作為容錯移轉組的成員）上負載平衡唯讀工作負載，請確保在主資料庫和異地複製次要資料庫上啟用讀取橫向擴展。 此配置將確保在容錯移轉後應用程式連接到新主資料庫時，相同的負載平衡體驗將繼續。 如果您要連線到啟用讀取縮放的異地複寫次要資料庫，則會使用與路由傳送主要資料庫上連線的相同方式，將設定 `ApplicationIntent=ReadOnly` 的工作階段路由傳送至其中一個複本。  未設定 `ApplicationIntent=ReadOnly` 的工作階段會路由傳送至異地複寫次要的主要複本，這也是唯讀狀態。 由於異地複製的次要資料庫的終結點與主資料庫不同，因此從歷史上看，訪問次要資料庫不需要設置`ApplicationIntent=ReadOnly`。 為了確保回溯相容性，`sys.geo_replication_links` DMV 會顯示 `secondary_allow_connections=2` (允許所有用戶端連線)。

> [!NOTE]
> 不支援在次要資料庫的本機複本之間迴圈或任何其他負載平衡路由。

## <a name="next-steps"></a>後續步驟

- 有關 SQL 資料庫超大規模產品/服務的資訊，請參閱[超大規模服務層](./sql-database-service-tier-hyperscale.md)。
