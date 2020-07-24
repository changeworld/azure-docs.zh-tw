---
title: 如何 audit Azure Cosmos DB 控制平面作業
description: 瞭解如何在 Azure Cosmos DB 中，以「新增區域」、「更新輸送量」、「區域容錯移轉」、「新增 VNet 等等」的方式來審核控制平面作業。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/25/2020
ms.author: sngun
ms.openlocfilehash: ae1d2743934c5ae8df9f2a1514bdda9b34262b9d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023682"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>如何 audit Azure Cosmos DB 控制平面作業

Azure Cosmos DB 中的控制平面是 RESTful 服務，可讓您對 Azure Cosmos 帳戶執行多樣化的作業集。 它會公開公用資源模型（例如：資料庫、帳戶）和各種作業，讓終端使用者在資源模型上執行動作。 控制平面作業包含 Azure Cosmos 帳戶或容器的變更。 例如，像是建立 Azure Cosmos 帳戶、新增區域、更新輸送量、區域容錯移轉、新增 VNet 等作業，都是一些控制平面作業。 本文說明如何在 Azure Cosmos DB 中，審核控制平面作業。 您可以使用 Azure CLI、PowerShell 或 Azure 入口網站在 Azure Cosmos 帳戶上執行控制平面作業，而針對容器，請使用 Azure CLI 或 PowerShell。

以下是一些範例案例，其中一些審核控制平面作業很有用：

* 當您的 Azure Cosmos 帳戶的防火牆規則已修改時，您想要收到警示。 若要找出未經授權修改的規則，以管理 Azure Cosmos 帳戶的網路安全性並採取快速動作，則必須發出警示。

* 您想要在 Azure Cosmos 帳戶中新增或移除新的區域時收到警示。 新增或移除區域會影響帳單和資料主權的需求。 此警示可協助您偵測在帳戶上意外新增或移除區域。

* 您想要從診斷記錄中取得已變更內容的更多詳細資料。 例如，VNet 已變更。

## <a name="disable-key-based-metadata-write-access"></a>停用以金鑰為基礎的中繼資料寫入存取

在 Azure Cosmos DB 中，您必須先停用帳戶的以金鑰為基礎的中繼資料寫入存取權，才能在中審核控制平面作業。 停用金鑰型中繼資料寫入權限時，會防止透過帳戶金鑰連接到 Azure Cosmos 帳戶的用戶端存取帳戶。 您可以藉由將屬性設定為 true 來停用寫入存取 `disableKeyBasedMetadataWriteAccess` 。 設定此屬性之後，任何資源的變更都可以從具有適當角色型存取控制（RBAC）角色和認證的使用者發生。 若要深入瞭解如何設定此屬性，請參閱[防止來自 sdk 的變更](role-based-access-control.md#prevent-sdk-changes)一文。 

開啟之後 `disableKeyBasedMetadataWriteAccess` ，如果以 SDK 為基礎的用戶端執行建立或更新作業，*則會傳回 Azure Cosmos DB 端點，而不允許資源 ' ContainerNameorDatabaseName ' 上的作業 ' POST '* 錯誤。 您必須為您的帳戶開啟這類作業的存取權，或透過 Azure Resource Manager、Azure CLI 或 Azure PowerShell 執行建立/更新作業。 若要切換回，請使用 Azure CLI （如[防止 COSMOS SDK 中的變更](role-based-access-control.md#prevent-sdk-changes)一文所述），將 disableKeyBasedMetadataWriteAccess 設定為**false** 。 請務必將的值變更 `disableKeyBasedMetadataWriteAccess` 為 false，而不是 true。

關閉中繼資料寫入權限時，請考慮下列幾點：

* 評估並確保您的應用程式不會使用 SDK 或帳戶金鑰來進行中繼資料呼叫，以變更上述資源（例如，建立集合、更新輸送量 ...）。

* 目前，Azure 入口網站會使用帳戶金鑰進行中繼資料作業，因此將會封鎖這些作業。 或者，使用 Azure CLI、Sdk 或 Resource Manager 範本部署來執行這類作業。

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>啟用控制平面作業的診斷記錄

您可以使用 Azure 入口網站來啟用控制平面作業的診斷記錄。 啟用之後，診斷記錄會將作業記錄為一對啟動和完成事件，並提供相關的詳細資料。 例如， *RegionFailoverStart*和*RegionFailoverComplete*將會完成區域容錯移轉事件。

使用下列步驟來啟用控制平面作業的記錄功能：

1. 登入 [Azure 入口網站](https://portal.azure.com)，並導覽至您的 Azure Cosmos 帳戶。

1. 開啟 [**診斷設定**] 窗格，提供要建立之記錄檔的**名稱**。

1. 針對 [記錄類型] 選取 [ **ControlPlaneRequests** ]，然後選取 [**傳送至 log Analytics** ] 選項。

您也可以將記錄儲存在儲存體帳戶中，或串流至事件中樞。 本文說明如何將記錄傳送到 log analytics，然後加以查詢。 啟用之後，需要幾分鐘的時間，診斷記錄才會生效。 可以追蹤在該點之後執行的所有控制平面作業。 下列螢幕擷取畫面顯示如何啟用控制平面記錄：

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="啟用控制平面要求記錄":::

## <a name="view-the-control-plane-operations"></a>觀看控制平面作業

開啟記錄功能之後，請使用下列步驟來追蹤特定帳戶的作業：

1. 登入[Azure 入口網站](https://portal.azure.com)。

1. 從左側導覽中開啟 [**監視**] 索引標籤，然後選取 [**記錄**] 窗格。 它會開啟一個 UI，您可以在其中使用範圍內的特定帳戶輕鬆地執行查詢。 執行下列查詢來查看控制平面記錄：

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

當 Azure Cosmos 帳戶的一致性層級變更時，下列螢幕擷取畫面會捕捉記錄：

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="新增 VNet 時的控制平面記錄":::

下列螢幕擷取畫面會在建立 Cassandra 帳戶的 keyspace 或資料表時，以及在更新輸送量時，捕捉記錄。 資料庫上的建立和更新作業的控制平面記錄，而且容器會分開記錄，如下列螢幕擷取畫面所示：

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="更新輸送量時的控制平面記錄":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>找出與特定作業相關聯的身分識別

如果您想要進一步進行進一步的調試，可以使用活動識別碼或作業的時間戳記，識別**活動記錄**中的特定作業。 時間戳記用於某些未明確傳遞活動識別碼的 Resource Manager 用戶端。 活動記錄檔會提供起始此作業的身分識別詳細資料。 下列螢幕擷取畫面顯示如何使用活動識別碼，並在活動記錄中尋找與其相關聯的作業：

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="使用活動識別碼並尋找作業":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Azure Cosmos 帳戶的控制平面作業

以下是可在帳戶層級使用的控制平面作業。 大部分的作業會在帳戶層級進行追蹤。 這些作業可做為 Azure 監視器中的計量：

* 已新增區域
* 區域已移除
* 帳戶已刪除
* 區域已故障切換
* 已建立帳戶
* 已刪除虛擬網路
* 已更新帳戶網路設定
* 已更新帳戶複寫設定
* 帳戶金鑰已更新
* 已更新帳戶備份設定
* 已更新帳戶診斷設定

## <a name="control-plane-operations-for-database-or-containers"></a>資料庫或容器的控制平面作業

以下是可在資料庫和容器層級使用的控制平面作業。 這些作業可做為 Azure 監視器中的計量：

* SQL Database 建立
* SQL Database 更新
* 已更新 SQL Database 輸送量
* SQL Database 刪除
* 已建立 SQL 容器
* 已更新 SQL 容器
* SQL 容器輸送量已更新
* SQL 容器已刪除
* 已建立 Cassandra Keyspace
* 已更新 Cassandra Keyspace
* 已更新 Cassandra Keyspace 輸送量
* 已刪除 Cassandra Keyspace
* 已建立 Cassandra 資料表
* Cassandra 資料表已更新
* 已更新 Cassandra 資料表輸送量
* 已刪除 Cassandra 資料表
* 已建立 Gremlin 資料庫
* 已更新 Gremlin 資料庫
* 已更新 Gremlin 資料庫輸送量
* 已刪除 Gremlin 資料庫
* 已建立 Gremlin 圖形
* Gremlin 圖已更新
* 已更新 Gremlin 圖形輸送量
* 已刪除 Gremlin 圖形
* 已建立 Mongo 資料庫
* 已更新 Mongo 資料庫
* 已更新 Mongo 資料庫輸送量
* 已刪除 Mongo 資料庫
* 已建立 Mongo 收集
* 已更新 Mongo 集合
* 已更新 Mongo 集合輸送量
* 已刪除 Mongo 集合
* 已建立 AzureTable 資料表
* AzureTable 資料表已更新
* 已更新 AzureTable 資料表輸送量
* 已刪除 AzureTable 資料表

## <a name="diagnostic-log-operations"></a>診斷記錄作業

以下是不同作業的診斷記錄中的操作名稱：

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

針對 API 特定作業，會使用下列格式來命名操作：

* ApiKind + ApiKindResourceType + OperationType
* ApiKind + ApiKindResourceType + "輸送量" + operationType

**範例** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

*ResourceDetails*屬性包含整個資源主體做為要求裝載，且包含所有要求更新的屬性。

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

## <a name="next-steps"></a>接下來的步驟

* [探索適用於 Azure Cosmos DB 的 Azure 監視器](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [使用 Azure Cosmos DB 中的計量進行監視及偵錯](use-metrics.md)
