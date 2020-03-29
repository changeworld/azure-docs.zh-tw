---
title: 使用大型資料集
description: 瞭解如何在使用 Azure 資源圖時獲取、格式化、頁面和跳過大型資料集中的記錄。
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064733"
---
# <a name="working-with-large-azure-resource-data-sets"></a>使用大型 Azure 資源資料集

Azure Resource Graph 是設計來使用和取得 Azure 環境中資源的相關資訊。 即使在查詢數千筆記錄時，Resource Graph 還是能夠快速取得此資料。 Resource Graph 有數個選項可用來使用這些大型資料集。

有關在高頻下處理查詢的指導，請參閱[限制請求指南](./guidance-for-throttled-requests.md)。

## <a name="data-set-result-size"></a>資料集結果大小

根據預設，Resource Graph 會將所有查詢限制為只傳回 **100** 筆記錄。 此控制項可保護使用者和服務，以防止會產生大型資料集的非預期查詢。 此事件大多會在客戶利用符合其特定需求的方式來實驗查詢以尋找並篩選資源時發生。 此控制項不同於使用 [top](/azure/kusto/query/topoperator) 或 [limit](/azure/kusto/query/limitoperator) Azure 資料總管語言運算子來限制結果。

> [!NOTE]
> 使用**First**時，建議使用`asc`或`desc`的至少一列對結果進行排序。 如果不排序，返回的結果是隨機的，不可重複。

預設限制可以透過與 Resource Graph 互動的所有方法來覆寫。 下列範例示範如何將資料集大小限制變更為 _200_：

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

在 [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) 中，此控制項為 **$top** 且屬於 **QueryRequestOptions**。

「限制最嚴格」__ 的控制項將會勝出。 例如，如果您的查詢會使用 **top** 或 **limit** 運算子並產生比 **First** 還多的記錄，則傳回的記錄數目上限會等於 **First**。 同樣地，如果 **top** 或 **limit** 小於 **First**，傳回的記錄集會是小於 **top** 或 **limit** 所設定的值。

**First** 目前具有最大允許值 _5000_。

## <a name="skipping-records"></a>略過記錄

使用大型資料集的下一個選項是 **Skip** 控制項。 此控制項可讓查詢先跳過或略過所定義的記錄數目，然後再傳回結果。 **Skip** 適用於以有意義的方式排序結果的查詢，其目的是取得位於結果集中間某處的記錄。 如果所需的結果位於所傳回資料集的結尾處，則使用不同的排序設定，並改為從資料集頂端擷取結果會更有效率。

> [!NOTE]
> 使用**Skip**時，建議對結果排序至少一列，其中 有`asc`或`desc`。 如果不排序，返回的結果是隨機的，不可重複。

下列範例示範如何略過查詢所產生的前 _10_ 筆記錄，改為從所傳回結果集的第 11 筆記錄開始：

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

在 [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) 中，此控制項為 **$skip** 且屬於 **QueryRequestOptions**。

## <a name="paging-results"></a>分頁結果

當需要將結果集分解為較小的記錄集進行處理，或者由於結果集將超過_1000_個返回的記錄的最大允許值時，請使用分頁。 [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse** 會提供值來表示已將結果集分解：**resultTruncated** 和 **$skipToken**。
**resultTruncated** 是一個布林值，如果回應中有其他未傳回的記錄，則會通知取用者。 在 **count** 屬性小於 **totalRecords** 屬性時，也可識別出此條件。 **totalRecords** 會定義符合查詢的記錄筆數。

 結果如果由於沒有`id`列而禁用或無法進行分頁，或者可用資源少於查詢請求的資源，則**Runcated**為**true。** 當**結果為** **true**時，不設置 **$skipToken**屬性。

以下示例演示如何**跳過**前 3000 條記錄，並在使用 Azure CLI 和 Azure PowerShell 跳過這些記錄後返回**前**1000 條記錄：

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> 查詢必須**投影****識別碼**欄位，才能使分頁運作。 如果查詢中缺少該回應，則回應不包括 **$skipToken**。

如需範例，請參閱 REST API 文件中的[下一頁查詢](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query)。

## <a name="formatting-results"></a>格式化結果

資源圖查詢的結果以兩種格式提供：_表_和_物件陣列_。 作為請求選項的一部分，格式配置了**結果格式**參數。 _表_格式是**結果格式**的預設值。

預設情況下，Azure CLI 的結果在 JSON 中提供。 預設情況下，Azure PowerShell 中的結果是**PSCustomObject，** 但可以使用`ConvertTo-Json`Cmdlet 快速轉換為 JSON。 對於其他 SDK，可以配置查詢結果以輸出_ObjectArray_格式。

### <a name="format---table"></a>格式 - 表

預設格式_表_返回的 JSON 格式旨在突出顯示查詢返回的屬性的列設計和行值。 此格式與結構化表或試算表中定義的資料非常相似，這些列首先標識，然後每行表示與這些列對齊的資料。

下面是具有_表_格式的查詢結果示例：

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>格式 - 物件陣列

_ObjectArray_格式還以 JSON 格式返回結果。 但是，此設計與 JSON 中常見的鍵/值對關係保持一致，其中列和行資料在陣列組中匹配。

下面是具有_ObjectArray_格式的查詢結果示例：

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

下面是一些設置**結果格式**以使用_ObjectArray_格式的示例：

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>後續步驟

- 請參閱[初學者查詢](../samples/starter.md)中正在使用的語言。
- 請參閱[高級查詢](../samples/advanced.md)中的高級用途。
- 深入了解如何[探索資源](explore-resources.md)。