---
title: 如何 audit Azure Cosmos DB 控制平面作業
description: 瞭解如何在 Azure Cosmos DB 中審核控制平面作業，例如新增區域、更新輸送量、區域容錯移轉、新增 VNet 等等。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 08cc3b08611947ac32973b2dfb01060140dc0798
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743891"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>如何 audit Azure Cosmos DB 控制平面作業

Azure Cosmos DB 中的控制平面是 RESTful 服務，可讓您在 Azure Cosmos 帳戶上執行不同的作業集。 它會公開公用資源模型 (例如：資料庫、帳戶) 和各種作業，以供終端使用者在資源模型上執行動作。 控制平面作業包括對 Azure Cosmos 帳戶或容器的變更。 例如，建立 Azure Cosmos 帳戶、新增區域、更新輸送量、區域容錯移轉、新增 VNet 等作業，都是一些控制平面作業。 本文說明如何在 Azure Cosmos DB 中審核控制平面作業。 您可以使用 Azure CLI、PowerShell 或 Azure 入口網站在 Azure Cosmos 帳戶上執行控制平面作業，而針對容器，請使用 Azure CLI 或 PowerShell。

以下是一些範例案例，其中一些審核控制平面作業很有説明：

* 當您的 Azure Cosmos 帳戶的防火牆規則遭到修改時，您想要收到警示。 若要對管理 Azure Cosmos 帳戶網路安全性的規則找出未經授權的修改，並採取快速動作，則需要警示。

* 您想要在 Azure Cosmos 帳戶中新增或移除新區域時收到警示。 新增或移除區域對於計費和資料主權需求都有影響。 此警示可協助您偵測到帳戶上的區域意外新增或移除。

* 您想要從診斷記錄取得已變更的詳細資料。 例如，VNet 已變更。

## <a name="disable-key-based-metadata-write-access"></a>停用金鑰型中繼資料寫入存取

在 Azure Cosmos DB 中審核控制平面作業之前，請先停用帳戶上的金鑰型中繼資料寫入存取權。 停用金鑰型中繼資料寫入存取時，透過帳戶金鑰連接至 Azure Cosmos 帳戶的用戶端，將無法存取該帳戶。 您可以藉由將屬性設定為 true 來停用寫入存取 `disableKeyBasedMetadataWriteAccess` 。 設定此屬性之後，任何資源的變更都可以從具有適當角色型存取控制的使用者發生， (RBAC) 角色和認證。 若要深入瞭解如何設定此屬性，請參閱 [防止 sdk 的變更](role-based-access-control.md#prevent-sdk-changes) 文章。 

開啟之後 `disableKeyBasedMetadataWriteAccess` ，如果以 SDK 為基礎的用戶端執行建立或更新作業，則 *不允許在資源 ' ContainerNameorDatabaseName ' 上* 的「作業 ' POST '」傳回 Azure Cosmos DB 端點。 您必須為您的帳戶開啟這類作業的存取權，或透過 Azure Resource Manager、Azure CLI 或 Azure PowerShell 來執行建立/更新作業。 若要切換回，請使用「[防止 COSMOS SDK 的變更](role-based-access-control.md#prevent-sdk-changes)」一文所述的 Azure CLI，將 disableKeyBasedMetadataWriteAccess 設定為**false** 。 請務必將的值變更 `disableKeyBasedMetadataWriteAccess` 為 false，而不是 true。

關閉中繼資料寫入存取權時，請考慮下列幾點：

* 評估並確定您的應用程式不會使用 SDK 或帳戶金鑰，進行變更上述資源的中繼資料呼叫 (例如，建立集合、更新輸送量 ... ) 。

* 目前，Azure 入口網站使用帳戶金鑰進行中繼資料作業，因此將會封鎖這些作業。 或者，您也可以使用 Azure CLI、Sdk 或 Resource Manager 範本部署來執行這類作業。

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>啟用控制平面作業的診斷記錄

您可以使用 Azure 入口網站來啟用控制平面作業的診斷記錄。 啟用之後，診斷記錄會將作業記錄為一對具有相關詳細資料的開始和完成事件。 例如， *RegionFailoverStart* 和 *RegionFailoverComplete* 將會完成區域容錯移轉事件。

使用下列步驟來啟用控制平面作業的記錄：

1. 登入 [Azure 入口網站](https://portal.azure.com)，並導覽至您的 Azure Cosmos 帳戶。

1. 開啟 [ **診斷設定** ] 窗格，提供要建立之記錄的 **名稱** 。

1. 選取 [ **ControlPlaneRequests** ] 作為 [記錄類型]，然後選取 [ **傳送至 log Analytics** ] 選項。

您也可以將記錄儲存在儲存體帳戶或串流至事件中樞。 本文說明如何將記錄傳送至 log analytics，然後進行查詢。 啟用之後，需要幾分鐘的時間，診斷記錄才會生效。 可以追蹤在該點之後執行的所有控制平面作業。 下列螢幕擷取畫面顯示如何啟用控制平面記錄：

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="啟用控制平面要求記錄":::

## <a name="view-the-control-plane-operations"></a>查看控制平面作業

開啟記錄之後，請使用下列步驟來追蹤特定帳戶的作業：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 開啟左側導覽中的 [ **監視** ] 索引標籤，然後選取 [ **記錄** ] 窗格。 它會開啟 UI，您可以在其中使用範圍內的特定帳戶輕鬆地執行查詢。 執行下列查詢以查看控制平面記錄：

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

下列螢幕擷取畫面會在 Azure Cosmos 帳戶的一致性層級變更時捕捉記錄：

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="啟用控制平面要求記錄":::

下列螢幕擷取畫面會在建立 Cassandra 帳戶的 keyspace 或資料表，以及更新輸送量時，捕捉記錄。 在資料庫上建立和更新作業的控制平面記錄和容器會分開記錄，如下列螢幕擷取畫面所示：

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="啟用控制平面要求記錄":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>識別與特定作業相關聯的身分識別

如果您想要進一步進行偵錯工具，您可以使用活動識別碼或作業的時間戳記，來識別 **活動記錄** 中的特定作業。 某些 Resource Manager 不會明確傳遞活動識別碼的用戶端會使用時間戳。 活動記錄會提供有關用來起始作業之身分識別的詳細資料。 下列螢幕擷取畫面顯示如何使用活動識別碼，並在活動記錄中尋找與其相關聯的作業：

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="啟用控制平面要求記錄":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Azure Cosmos 帳戶的控制平面作業

以下是帳戶層級可用的控制平面作業。 大部分的作業會在帳戶層級進行追蹤。 這些作業可作為 Azure 監視器中的計量：

* 已新增區域
* 已移除區域
* 帳戶已刪除
* 區域已容錯移轉
* 已建立帳戶
* 虛擬網路已刪除
* 帳戶網路設定已更新
* 帳戶複寫設定已更新
* 帳戶金鑰已更新
* 帳戶備份設定已更新
* 帳戶診斷設定已更新

## <a name="control-plane-operations-for-database-or-containers"></a>資料庫或容器的控制平面作業

以下是資料庫和容器層級可用的控制平面作業。 這些作業可作為 Azure 監視器中的計量：

* SQL Database 建立
* SQL Database 更新
* 已更新 SQL Database 輸送量
* SQL Database 已刪除
* 已建立 SQL 容器
* SQL 容器已更新
* SQL 容器輸送量已更新
* SQL 容器已刪除
* 已建立 Cassandra Keyspace
* Cassandra Keyspace 已更新
* 已更新 Cassandra Keyspace 輸送量
* Cassandra Keyspace 已刪除
* 已建立 Cassandra 資料表
* Cassandra 資料表已更新
* Cassandra 資料表輸送量已更新
* Cassandra 資料表已刪除
* 已建立 Gremlin 資料庫
* Gremlin 資料庫已更新
* 已更新 Gremlin 資料庫輸送量
* Gremlin 資料庫已刪除
* 已建立 Gremlin 圖形
* Gremlin 圖已更新
* 已更新 Gremlin 圖輸送量
* 已刪除 Gremlin 圖形
* 已建立 Mongo 資料庫
* Mongo 資料庫已更新
* 已更新 Mongo 資料庫輸送量
* Mongo 資料庫已刪除
* 已建立 Mongo 收集
* Mongo 集合已更新
* 已更新 Mongo 集合輸送量
* 已刪除 Mongo 集合
* 已建立 AzureTable 資料表
* AzureTable 資料表已更新
* AzureTable 資料表輸送量已更新
* AzureTable 資料表已刪除

## <a name="diagnostic-log-operations"></a>診斷記錄作業

下列是不同作業的診斷記錄中的作業名稱：

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

針對 API 特定的作業，作業會以下列格式命名：

* ApiKind + ApiKindResourceType + OperationType
* ApiKind + ApiKindResourceType + "輸送量" + operationType

**範例** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

*ResourceDetails*屬性包含整個資源主體作為要求承載，而且包含所有要求更新的屬性

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>控制平面作業的診斷記錄查詢

以下是取得控制平面作業診斷記錄的一些範例：

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersThroughputUpdate"
```

查詢以取得 activityId 和起始容器刪除作業的呼叫端：

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

取得索引或 ttl 更新的查詢。 然後，您可以比較此查詢的輸出與先前的更新，以查看索引或 ttl 的變更。

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**輸出：**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>後續步驟

* [探索適用於 Azure Cosmos DB 的 Azure 監視器](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [使用 Azure Cosmos DB 中的計量進行監視及偵錯](use-metrics.md)
