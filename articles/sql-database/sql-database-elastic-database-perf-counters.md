---
title: 用於跟蹤分片映射管理器的效能計數器
description: ShardMapManager 類別與資料相依路由效能計數器
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: de481dad9dd39b301a21142c67b1baf2209f76e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823892"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>創建效能計數器以跟蹤分片映射管理器的性能

效能計數器用於跟蹤[與資料相關的路由](sql-database-elastic-scale-data-dependent-routing.md)操作的性能。 這些計數器可在「彈性資料庫：分區管理」類別下的「效能監視器」中存取。

您可以擷取[分區對應管理員](sql-database-elastic-scale-shard-map-management.md)的效能，特別是在使用[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)時。 計數器是使用 Microsoft.Azure.SqlDatabase.ElasticScale.Client 類別的方法建立。  


**如需最新版本** ：請瀏覽 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 另請參閱 [將應用程式升級以使用最新的彈性資料庫用戶端程式庫](sql-database-elastic-scale-upgrade-client-library.md)。

## <a name="prerequisites"></a>Prerequisites

* 若要建立效能類別和計數器，使用者必須屬於裝載應用程式的電腦上的本機 **Administrators** 群組。  
* 若要建立效能計數器執行個體和更新計數器，使用者必須是 **Administrators** 或 **Performance Monitor Users** 群組的成員。

## <a name="create-performance-category-and-counters"></a>建立效能類別和計數器

若要建立計數器，請呼叫 [ShardMapManagementFactory 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)的 CreatePeformanceCategoryAndCounters 方法。 只有系統管理員可以執行這個方法︰

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

您也可以使用 [這個](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell 指令碼來執行方法。
這個方法會建立下列效能計數器︰  

* **快取對應**︰針對分區對應快取的對應數目。
* **DDR 作業數/秒**︰分區對應的資料相依路由作業速率。 對 [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) 的呼叫導致順利連接到目的地分區時，就會更新此計數器。
* **對應查閱快取點擊/秒**︰分區對應中對應的成功快取查閱作業的速率。
* **對應查閱快取遺失/秒**︰分區對應中對應的失敗快取查閱作業的速率。
* **快取中的對應新增或更新/秒**︰分區對應的快取中新增或更新對應的速率。
* **從快取中移除對應/秒**︰從分區對應的快取中移除對應的速率。

效能計數器會在每個程序針對每個快取的分區對應建立。  

## <a name="notes"></a>注意

下列事件會觸發效能計數器的建立︰  

* 在 ShardMapManager 包含任何分區對應的情況下，使用[積極式載入](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)將 [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) 初始化。 這些包括 [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) 和 [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) 方法。
* 成功查閱分區對應 (使用 [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx)、[GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) 或 [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx))。
* 使用 CreateShardMap() 成功建立分區對應。

效能計數器會由分區對應和對應上執行的所有快取作業更新。 使用 DeleteShardMap() 成功移除分區對應，會導致刪除效能計數器執行個體。  

## <a name="best-practices"></a>最佳作法

* 建立效能類別和計數器應該僅在建立 ShardMapManager 物件之前執行一次。 每次執行命令 CreatePerformanceCategoryAndCounters() 都會清除先前的計數器 (遺失所有執行個體報告的資料)，並建立新的計數器。  
* 每個程序都會建立效能計數器執行個體。 任何應用程式當機或從快取移除分區對應都會導致刪除效能計數器執行個體。  

### <a name="see-also"></a>另請參閱

[彈性資料庫功能概觀](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
