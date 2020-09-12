---
title: 了解查詢語言
description: 描述 Resource Graph 資料表，以及可與 Azure Resource Graph 搭配使用的可用 Kusto 資料類型、運算子和函式。
ms.date: 08/24/2020
ms.topic: conceptual
ms.openlocfilehash: 65304ca1241b2c8a1f9541580e7ee8434dd5b6eb
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426396"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>了解 Azure Resource Graph 查詢語言

Azure Resource Graph 查詢語言支援多個運算子與函式。 每個都是根據 [Kusto 查詢語言 (KQL)](/azure/kusto/query/index) 運作和操作。 若要了解 Resource Graph 所使用的查詢語言，請從 [KQL 的教學課程](/azure/kusto/query/tutorial)開始。

本文涵蓋 Resource Graph 支援的語言元件：

- [Resource Graph 資料表](#resource-graph-tables)
- [Resource Graph 自訂語言元素](#resource-graph-custom-language-elements)
- [支援的 KQL 語言元素](#supported-kql-language-elements)
- [查詢的範圍](#query-scope)
- [逸出字元](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph 資料表

Resource Graph 針對其儲存的資料，提供數個數據表，Azure Resource Manager 資源類型和其屬性。 這些資料表可以與 `join` 或 `union` 運算子搭配使用，以取得相關資源類型中的屬性。 以下是 Resource Graph 中可用的資料表清單：

|Resource Graph 資料表 |描述 |
|---|---|
|資源 |如果未在查詢中定義任何資料表，則使用預設資料表。 大部分的 Resource Manager 資源類型和屬性都在這裡。 |
|ResourceContainers |包含訂用帳戶 (預覽狀態 -- `Microsoft.Resources/subscriptions`) 和資源群組 (`Microsoft.Resources/subscriptions/resourcegroups`) 資源類型和資料。 |
|AdvisorResources |包含與 `Microsoft.Advisor`「相關」的資源。 |
|AlertsManagementResources |包含與 `Microsoft.AlertsManagement`「相關」的資源。 |
|GuestConfigurationResources |包含與 `Microsoft.GuestConfiguration`「相關」的資源。 |
|HealthResources |包含與 `Microsoft.ResourceHealth`「相關」的資源。 |
|MaintenanceResources |包含與 `Microsoft.Maintenance`「相關」的資源。 |
|SecurityResources |包含與 `Microsoft.Security`「相關」的資源。 |

如需包含資源類型的完整清單，請參閱[參考：支援的資料表和資源類型](../reference/supported-tables-resources.md)。

> [!NOTE]
> _Resources_ 是預設資料表。 查詢 _Resources_ 資料表時，除非使用 `join` 或 `union`，否則不需要提供資料表名稱。 不過，建議的做法是在查詢中一律包含初始資料表。

使用入口網站中的 Resource Graph Explorer，探索每個資料表中可用的資源類型。 或者，使用 `<tableName> | distinct type` 之類的查詢來取得給定 Resource Graph 資料表所支援的資源類型清單，而該清單存在於您的環境中。

下列查詢顯示簡單的 `join`。 查詢結果會將這些資料行結合在一起，而聯結資料表中任何重複的資料行名稱 (此範例中的 _ResourceContainers_) 都會附加 **1**。 當 _ResourceContainers_ 資料表同時具有訂用帳戶和資源群組的類型，這兩種類型都可能用來從 _resources_ 資料表聯結至資源。

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

下列查詢顯示 `join` 的更複雜用法。 此查詢會將聯結的資料表限制為訂用帳戶資源，並利用 `project` 使其僅包含原始欄位 _subscriptionId_ 和已重新命名為 _SubName_ 的 _name_ 欄位。 欄位重新命名可避免 `join` 將其新增為 _name1_，因為此欄位已經存在於 _Resources_ 中。 原始資料表會使用 `where` 進行篩選，而下列 `project` 包含兩個資料表的資料行。 查詢結果是單一金鑰保存庫顯示類型、金鑰保存庫的名稱，以及其所在的訂用帳戶名稱。

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> 當使用 `project` 來限制 `join` 結果時，`join` 用來使兩個資料表相關的屬性 (上述範例中的 _subscriptionId_) 必須包含在 `project` 中。

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>擴充屬性 (預覽) 

作為 _預覽_ 功能，Resource Graph 中的某些資源類型具有其他類型相關屬性，可用於查詢超出 Azure Resource Manager 所提供的屬性。 這組值（稱為 _擴充屬性_）存在於中支援的資源類型上 `properties.extended` 。 若要查看哪些資源類型具有 _擴充屬性_，請使用下列查詢：

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

範例：取得虛擬機器計數的方式 `instanceView.powerState.code` ：

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Resource Graph 自訂語言元素

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a> (預覽) 的共用查詢語法

作為預覽功能，您可以直接在 Resource Graph 查詢中存取 [共用查詢](../tutorials/create-share-query.md) 。 此案例可讓您建立標準查詢作為共用查詢並重複使用。 若要在 Resource Graph 查詢內呼叫共用查詢，請使用 `{{shared-query-uri}}` 語法。 共用查詢的 URI 是該查詢之 [**設定**] 頁面上的共用查詢_資源識別碼_。 在此範例中，我們的共用查詢 URI 為 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` 。
此 URI 會指向訂用帳戶、資源群組，以及我們想要在另一個查詢中參考之共用查詢的完整名稱。 這項查詢與在 [教學課程：建立和共用查詢](../tutorials/create-share-query.md)中建立的查詢相同。

> [!NOTE]
> 您無法將參考共用查詢的查詢儲存為共用查詢。

範例1：僅使用共用查詢

此 Resource Graph 查詢的結果與儲存在共用查詢中的查詢相同。

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

範例2：將共用查詢包含在較大的查詢中

此查詢會先使用共用查詢，然後再使用 `limit` 進一步限制結果。

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>支援的 KQL 語言元素

Resource Graph 支援 KQL [資料類型](/azure/kusto/query/scalar-data-types/)、純量 [函數](/azure/kusto/query/scalarfunctions)、純量 [運算子](/azure/kusto/query/binoperators)和 [彙總函式](/azure/kusto/query/any-aggfunction)的子集。 Resource Graph 支援特定的[表格式運算子](/azure/kusto/query/queries)，其中有些具有不同的行為。

### <a name="supported-tabulartop-level-operators"></a>支援的表格式/最上層運算子

以下是 Resource Graph 支援的 KQL 表格式運算子清單與特定範例：

|KQL |Resource Graph 範例查詢 |注意 |
|---|---|---|
|[計數](/azure/kusto/query/countoperator) |[計算金鑰保存庫數目](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[顯示特定別名的相異值](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[依作業系統類型計算的虛擬機器計數](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[金鑰保存庫與訂用帳戶名稱](../samples/advanced.md#join) |支援的聯結類別：[innerunique](/azure/kusto/query/joinoperator#default-join-flavor)、[inner](/azure/kusto/query/joinoperator#inner-join)、[leftouter](/azure/kusto/query/joinoperator#left-outer-join)。 單一查詢中限制 3 個`join`。 不允許自訂聯結策略 (例如廣播聯結)。 可以在單一資料表內或在 _Resources_ 和 _ResourceContainers_ 資料表之間使用。 |
|[limit](/azure/kusto/query/limitoperator) |[列出所有公用 IP 位址](../samples/starter.md#list-publicip) |`take` 的同義字 |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | 舊版運算子，請改用 `mv-expand`。 _RowLimit_ 的上限為 400。 預設值為 128。 |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[列出具有特定寫入位置的 Cosmos DB](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ 的上限為 400。 預設值為 128。 |
|[order](/azure/kusto/query/orderoperator) |[列出依名稱排序的資源](../samples/starter.md#list-resources) |`sort` 的同義字 |
|[project](/azure/kusto/query/projectoperator) |[列出依名稱排序的資源](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[移除結果中的資料行](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[列出依名稱排序的資源](../samples/starter.md#list-resources) |`order` 的同義字 |
|[summarize](/azure/kusto/query/summarizeoperator) |[計算的 Azure 資源計數](../samples/starter.md#count-resources) |僅限簡化的第一頁 |
|[take](/azure/kusto/query/takeoperator) |[列出所有公用 IP 位址](../samples/starter.md#list-publicip) |`limit` 的同義字 |
|[top](/azure/kusto/query/topoperator) |[依名稱顯示前五個虛擬機器及其作業系統類型](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[將兩個查詢的結果合併成單一結果](../samples/advanced.md#unionresults) |允許的單一資料表：_T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _資料表_。 單一查詢中限制 3 個 `union` 支線。 不允許 `union` 支線資料表的模糊解析。 可以在單一資料表內或在 _Resources_ 和 _ResourceContainers_ 資料表之間使用。 |
|[where](/azure/kusto/query/whereoperator) |[顯示包含儲存體的資源](../samples/starter.md#show-storage) | |

## <a name="query-scope"></a>查詢範圍

查詢所傳回之資源的訂用帳戶範圍，取決於存取 Resource Graph 的方法。 Azure CLI 和 Azure PowerShell 根據授權使用者的內容，填入要包含在要求中的訂用帳戶清單。 您可以為每個訂用 **帳戶和** 訂用帳戶參數分別手動定義 **訂** 用帳戶清單。
在 REST API 和所有其他 Sdk 中，包含資源的訂用帳戶清單必須明確定義為要求的一部分。

作為 **預覽**版本，REST API 版本會 `2020-04-01-preview` 新增屬性，以將查詢的範圍設為 [管理群組](../../management-groups/overview.md)。 此預覽 API 也會讓訂用帳戶屬性成為選擇性的。 如果未定義管理群組或訂用帳戶清單，查詢範圍就是所有資源，包括已驗證的使用者可以存取的 [Azure Lighthouse](../../../lighthouse/concepts/azure-delegated-resource-management.md) 委派的資源。 新的 `managementGroupId` 屬性會採用管理群組識別碼，與管理群組的名稱不同。 當您 `managementGroupId` 指定時，會包含在指定的管理群組階層中或下的前5000訂用帳戶中的資源。 `managementGroupId` 無法與相同的時間使用 `subscriptions` 。

範例：查詢名為「我的管理群組」（識別碼為 ' myMG '）之管理群組階層內的所有資源。

- REST API URI

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- 要求本文

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>逸出字元

某些屬性名稱 (例如包含 `.` 或 `$` 的屬性) 必須在查詢中換行或逸出，否則屬性名稱會不正確地解譯，而且不會提供預期的結果。

- `.` - 將屬性名稱換行，如下所示：`['propertyname.withaperiod']`
  
  將屬性 _odata.type_ 換行的範例查詢：

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - 將屬性名稱中的字元逸出。 使用的逸出字元取決於 Resource Graph 從哪個殼層執行。

  - **bash** - `\`

    在 bash 中將屬性 _\$type_ 逸出的範例查詢：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - 不要將 `$` 字元逸出。

  - **PowerShell** - ``` ` ```

    在 PowerShell 中將屬性 _\$type_ 逸出的範例查詢：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](../samples/starter.md)中使用的語言。
- 請參閱[進階查詢](../samples/advanced.md)中的進階使用方式。
- 深入了解如何[探索資源](explore-resources.md)。
