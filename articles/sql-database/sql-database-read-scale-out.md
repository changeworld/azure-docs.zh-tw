---
title: 讀取複本上的查詢
description: 此 Azure SQL Database 可讓您使用唯讀複本的容量（稱為讀取相應放大）來負載平衡唯讀工作負載。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78206940"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>使用唯讀複本對唯讀查詢工作負載進行負載平衡

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

作為[高可用性架構](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)的一部分，Premium 和商務關鍵服務層級中的每個資料庫都會自動布建主要複本和數個次要複本。 次要複本會以與主要複本相同的計算大小進行布建。 **讀取相應放大**功能可讓您使用其中一個唯讀複本的容量來負載平衡 SQL Database 唯讀工作負載，而不是共用讀寫複本。 這種方式的唯讀工作負載將會與主要讀寫工作負載隔離，而且不會影響其效能。 此功能適用于包含邏輯上分隔唯讀工作負載（例如分析）的應用程式。 在 Premium 和商務關鍵性服務層級中，應用程式可以使用此額外容量來獲得效能優勢，而不需額外成本。

當至少建立一個次要複本時，超大規模資料庫服務層級也會提供**讀取**相應放大功能。 如果唯讀工作負載所需的資源超過一個次要複本上的可用性，則可以使用多個次要複本。 「基本」、「標準」和「一般用途」服務層級的高可用性架構不包含任何複本。 **讀取相應放大**功能在這些服務層級中無法使用。

下圖說明如何使用業務關鍵資料庫。

![唯讀複本](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

在新的 Premium、業務關鍵和超大規模資料庫資料庫上，預設會啟用讀取相應放大功能。 針對超大規模資料庫，預設會為新的資料庫建立一個次要複本。 如果您的 SQL 連接字串是以`ApplicationIntent=ReadOnly`進行設定，則該應用程式將由閘道重新導向至該資料庫的唯讀複本。 如需如何使用屬性的`ApplicationIntent`詳細資訊，請參閱[指定應用程式意圖](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果您想要確保應用程式連接到主要複本，而不論 SQL 連接`ApplicationIntent`字串中的設定為何，您必須在建立資料庫時或在改變其設定時，明確停用讀取相應放大。 例如，如果您將資料庫從標準或一般用途層升級至高階、業務關鍵或超大規模資料庫層，而且想要確定所有連線都會繼續移至主要複本，請停用讀取相應放大。如需如何停用它的詳細資訊，請參閱[啟用和停用讀取相應放大](#enable-and-disable-read-scale-out)。

> [!NOTE]
> 唯讀複本不支援查詢資料存放區、擴充的事件和 SQL Profiler 功能。

## <a name="data-consistency"></a>資料一致性

複本的其中一個優點是複本一律處於交易一致狀態，但在不同的時間點，不同的複本之間可能會有些微延遲。 讀取相應放大支援工作階段層級一致性。 這表示，如果唯讀會話在無法使用複本所造成的連線錯誤之後重新連線，它可能會被重新導向至與讀寫複本不是100% 最新的複本。 同樣地，如果應用程式使用讀寫會話寫入資料，並且使用唯讀會話立即讀取，則可能不會立即在複本上看到最新的更新。 延遲是因非同步交易記錄重做作業所造成。

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

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>監視和疑難排解唯讀複本

當連接到唯讀複本時，您可以使用`sys.dm_db_resource_stats` DMV 來存取效能計量。 若要存取查詢計劃統計資料， `sys.dm_exec_query_stats`請`sys.dm_exec_query_plan`使用`sys.dm_exec_sql_text` 、和 dmv。

> [!NOTE]
> 邏輯 master `sys.resource_stats`資料庫中的 DMV 會傳回主要複本的 CPU 使用率和儲存體資料。

## <a name="enable-and-disable-read-scale-out"></a>啟用和停用讀取縮放

在 Premium、業務關鍵和超大規模資料庫服務層級上，預設會啟用讀取相應放大。 在基本、標準或一般用途服務層級中，無法啟用讀取相應放大。 在設定了0個複本的超大規模資料庫資料庫上，會自動停用讀取相應放大。

您可以使用下列方法，在 Premium 或業務關鍵服務層級中的單一資料庫和彈性集區資料庫上，停用並重新啟用讀取相應放大。

> [!NOTE]
> 針對回溯相容性，可停用讀取向外延展的功能。

### <a name="azure-portal"></a>Azure 入口網站

您可以在 [**設定**資料庫] 分頁上管理讀取相應放大設定。

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager （RM）模組，但所有未來的開發都是針對 Az .Sql 模組。 AzureRM 模組會繼續收到錯誤修正，直到2020年12月為止。  Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 如需其相容性的詳細資訊，請參閱[新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

要在 Azure PowerShell 中管理讀取相應放大，必須使用 2016 年 12 月版的 Azure PowerShell 或更新版本。 如需最新 PowerShell 版本的相關資訊，請參閱 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

您可以藉由叫用[set-azsqldatabase 搭配](/powershell/module/az.sql/set-azsqldatabase)Cmdlet 並傳入所需的值– `Enabled`或`Disabled` --作為`-ReadScale`參數，在 Azure PowerShell 中停用或重新啟用讀取相應放大。

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

若要建立已停用讀取相應放大的資料庫，或變更現有資料庫的設定，請使用下列方法，並將`readScale`屬性設定為`Enabled`或`Disabled` ，如下列範例要求所示。

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

## <a name="using-tempdb-on-read-only-replica"></a>在唯讀複本上使用 TempDB

TempDB 資料庫不會複寫至唯讀複本。 每個複本都有自己的 TempDB 資料庫版本，它是在建立複本時所建立的。 它可確保 TempDB 是可更新的，而且可以在查詢執行期間修改。 如果您的唯讀工作負載相依于使用 TempDB 物件，您應該在查詢腳本中建立這些物件。

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>對異地複寫的資料庫使用讀取縮放

如果您使用讀取相應放大來負載平衡異地複寫之資料庫上的唯讀工作負載（例如，做為容錯移轉群組的成員），請確定主要和異地複寫的次要資料庫上都已啟用讀取相應放大。 此設定可確保當您的應用程式在容錯移轉之後連線到新的主要複本時，相同的負載平衡體驗會繼續進行。 如果您要連線到啟用讀取縮放的異地複寫次要資料庫，則會使用與路由傳送主要資料庫上連線的相同方式，將設定 `ApplicationIntent=ReadOnly` 的工作階段路由傳送至其中一個複本。  未設定 `ApplicationIntent=ReadOnly` 的工作階段會路由傳送至異地複寫次要的主要複本，這也是唯讀狀態。 由於異地複寫的次要資料庫具有與主資料庫不同的端點，因此在過去存取次要複本時，不需要設定`ApplicationIntent=ReadOnly`。 為了確保回溯相容性，`sys.geo_replication_links` DMV 會顯示 `secondary_allow_connections=2` (允許所有用戶端連線)。

> [!NOTE]
> 不支援在次要資料庫的本機複本之間進行迴圈配置資源或任何其他負載平衡路由。

## <a name="next-steps"></a>後續步驟

- 如需 SQL Database 超大規模資料庫供應專案的相關資訊，請參閱[超大規模資料庫服務層級](./sql-database-service-tier-hyperscale.md)。
