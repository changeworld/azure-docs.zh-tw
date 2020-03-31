---
title: 了解查詢語言
description: 描述資源圖表以及 Azure 資源圖可用的 Kusto 資料類型、運算子和函數。
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927488"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>了解 Azure Resource Graph 查詢語言

Azure Resource Graph 查詢語言支援多個運算子與函式。 每個工作和操作基於[Kusto 查詢語言 （KQL）](/azure/kusto/query/index)。 要瞭解資源圖使用的查詢語言，請從[KQL 教程](/azure/kusto/query/tutorial)開始。

本文介紹資源圖支援的語言元件：

- [資源圖表](#resource-graph-tables)
- [支援的 KQL 語言元素](#supported-kql-language-elements)
- [逸出字元](#escape-characters)

## <a name="resource-graph-tables"></a>資源圖表

資源圖為存儲的有關資源管理器資源類型及其屬性的資料提供了多個表。 這些表可用於`join`或`union`運算子，以便從相關資源類型獲取屬性。 下面是資源圖中可用的表清單：

|資源圖表 |描述 |
|---|---|
|資源 |預設表（如果查詢中未定義）。 大多數資源管理器資源類型和屬性都位於此處。 |
|資源容器 |包括訂閱（預覽中`Microsoft.Resources/subscriptions`） 和資源組`Microsoft.Resources/subscriptions/resourcegroups`（ ） 資源類型和資料。 |
|顧問資源 |包括與_的資源。_ `Microsoft.Advisor` |
|警報管理資源 |包括與_的資源。_ `Microsoft.AlertsManagement` |
|維護資源 |包括與_的資源。_ `Microsoft.Maintenance` |
|安全資源 |包括與_的資源。_ `Microsoft.Security` |

有關包含資源類型的完整清單，請參閱[參考：支援的表和資源類型](../reference/supported-tables-resources.md)。

> [!NOTE]
> _資源_是預設表。 查詢_參考資料_表時，除非或使用`join``union`表名稱，否則不需要提供表名稱。 但是，建議的做法是始終在查詢中包括初始表。

在門戶中使用資源圖資源管理器，瞭解每個表中可用的資源類型。 作為替代方法，請使用查詢，例如`<tableName> | distinct type`獲取給定資源圖表支援的資源類型清單，這些類型存在於您的環境中。

下面的查詢顯示了一個簡單的`join`。 查詢結果將列混合在一起，聯合資料表中的任何重複列名稱（本示例中的資源_容器_）都追加為**1**。 由於_資源容器_表具有訂閱和資源組的類型，因此可以使用任一類型從_資源_表聯接到資源。

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

下面的查詢顯示了 更複雜的使用`join`。 此查詢會將聯結的資料表限制為訂用帳戶資源，並利用 `project` 使其僅包含原始欄位 _subscriptionId_ 和已重新命名為 _SubName_ 的 _name_ 欄位。 欄位重命名避免將其`join`添加為_name1，_ 因為該欄位已存在於_參考資料_中。 原始資料表會使用 `where` 進行篩選，而下列 `project` 包含兩個資料表的資料行。 查詢結果是單一金鑰保存庫顯示類型、金鑰保存庫的名稱，以及其所在的訂用帳戶名稱。

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> `join`使用`project`限制結果時，必須包括`join`用於關聯兩個表的屬性（上述示例中的訂閱_subscriptionId_`project`Id）。

## <a name="supported-kql-language-elements"></a>支援的 KQL 語言元素

資源圖支援所有 KQL[資料類型](/azure/kusto/query/scalar-data-types/)、[標量函數](/azure/kusto/query/scalarfunctions)、[標量運算子](/azure/kusto/query/binoperators)和[彙總函式](/azure/kusto/query/any-aggfunction)。 資源圖支援特定的[表格運算子](/azure/kusto/query/queries)，其中一些運算子具有不同的行為。

### <a name="supported-tabulartop-level-operators"></a>支援的表格/頂級運算子

下面是資源圖支援的 KQL 表格運算子清單，該清單包含特定示例：

|KQL |資源圖依例查詢 |注意 |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[計算金鑰保存庫](../samples/starter.md#count-keyvaults) | |
|[不同](/azure/kusto/query/distinctoperator) |[顯示特定別名的不同值](../samples/starter.md#distinct-alias-values) | |
|[擴展](/azure/kusto/query/extendoperator) |[依作業系統類型計算的虛擬機器計數](../samples/starter.md#count-os) | |
|[加入](/azure/kusto/query/joinoperator) |[具有訂閱名稱的金鑰保存庫](../samples/advanced.md#join) |支援加入口味：[內唯一](/azure/kusto/query/joinoperator#default-join-flavor)，[內，](/azure/kusto/query/joinoperator#inner-join)[左外](/azure/kusto/query/joinoperator#left-outer-join)。 單個查詢中`join`限制 3。 不允許自訂聯接策略，如廣播聯接。 可以在單個表內或在 _"資源__和資源容器"_ 表之間使用。 |
|[limit](/azure/kusto/query/limitoperator) |[列出所有公用 IP 位址](../samples/starter.md#list-publicip) |同義字`take` |
|[mv 展開](/azure/kusto/query/mvexpandoperator) | | 改為使用`mv-expand`舊運算子。 _排限_最大 400。 預設值為 128。 |
|[mv 擴展](/azure/kusto/query/mvexpandoperator) |[列出具有特定寫入位置的宇宙 DB](../samples/advanced.md#mvexpand-cosmosdb) |_排限_最大 400。 預設值為 128。 |
|[以](/azure/kusto/query/orderoperator) |[列出依名稱排序的資源](../samples/starter.md#list-resources) |同義字`sort` |
|[專案](/azure/kusto/query/projectoperator) |[列出依名稱排序的資源](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[從結果中刪除列](../samples/advanced.md#remove-column) | |
|[排序](/azure/kusto/query/sortoperator) |[列出依名稱排序的資源](../samples/starter.md#list-resources) |同義字`order` |
|[總結](/azure/kusto/query/summarizeoperator) |[計算 Azure 資源](../samples/starter.md#count-resources) |僅簡化第一頁 |
|[採取](/azure/kusto/query/takeoperator) |[列出所有公用 IP 位址](../samples/starter.md#list-publicip) |同義字`limit` |
|[返回頁首](/azure/kusto/query/topoperator) |[依名稱顯示前五個虛擬機器及其作業系統類型](../samples/starter.md#show-sorted) | |
|[聯盟](/azure/kusto/query/unionoperator) |[將兩個查詢的結果合併為單個結果](../samples/advanced.md#unionresults) |允許的單一表 _：T_`| union`\[`kind=``inner`\|`outer`\]_Table_列名稱表 。 _ColumnName_ \] \[ `withsource=` 單個查詢中`union`3 條腿的限制。 `union`不允許模糊地解析腿表。 可以在單個表內或在 _"資源__和資源容器"_ 表之間使用。 |
|[where](/azure/kusto/query/whereoperator) |[顯示包含儲存體的資源](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>逸出字元

某些屬性名稱（如包含`.`或`$`的屬性名稱）必須在查詢中包裝或轉義，否則屬性名稱的解釋不正確，並且不提供預期結果。

- `.`- 將屬性名稱包裝如下：`['propertyname.withaperiod']`
  
  包裝屬性_odata.type_的依例查詢：

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- 轉義屬性名稱中的字元。 使用的逸出字元取決於從運行的 shell 資源圖。

  - **Bash** - `\`

    在 bash 中轉義屬性_\$類型_的依例查詢：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - 不要逃避`$`角色。

  - **電源外殼** - ``` ` ```

    轉義 PowerShell 中的屬性_\$類型_的依例查詢：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>後續步驟

- 請參閱[初學者查詢](../samples/starter.md)中正在使用的語言。
- 請參閱[高級查詢](../samples/advanced.md)中的高級用途。
- 深入了解如何[探索資源](explore-resources.md)。