---
title: 使用大型資料集
description: 瞭解如何在使用 Azure Resource Graph 時，取得、格式化、分頁和略過大型資料集中的記錄。
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 2c6aca0c468630cee79222bc77bdc20dc9d95b19
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259795"
---
# <a name="working-with-large-azure-resource-data-sets"></a>使用大型 Azure 資源資料集

Azure Resource Graph 是設計來使用和取得 Azure 環境中資源的相關資訊。 即使在查詢數千筆記錄時，Resource Graph 還是能夠快速取得此資料。 Resource Graph 有數個選項可用來使用這些大型資料集。

如需以較高頻率使用查詢的指引，請參閱[節流要求的指引](./guidance-for-throttled-requests.md)。

## <a name="data-set-result-size"></a>資料集結果大小

根據預設，Resource Graph 會將所有查詢限制為只傳回 **100** 筆記錄。 此控制項可保護使用者和服務，以防止會產生大型資料集的非預期查詢。 此事件大多會在客戶利用符合其特定需求的方式來實驗查詢以尋找並篩選資源時發生。 此控制項不同於使用 [top](/azure/kusto/query/topoperator) 或 [limit](/azure/kusto/query/limitoperator) Azure 資料總管語言運算子來限制結果。

> [!NOTE]
> **第一次**使用時，建議您至少使用一個具有 `asc` 或 `desc`的資料行來排序結果。 如果沒有排序，傳回的結果會是隨機的，而且無法重複。

預設限制可以透過與 Resource Graph 互動的所有方法來覆寫。 下列範例示範如何將資料集大小限制變更為 _200_：

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

在 [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) 中，此控制項為 **$top** 且屬於 **QueryRequestOptions**。

「限制最嚴格」的控制項將會勝出。 例如，如果您的查詢會使用 **top** 或 **limit** 運算子並產生比 **First** 還多的記錄，則傳回的記錄數目上限會等於 **First**。 同樣地，如果 **top** 或 **limit** 小於 **First**，傳回的記錄集會是小於 **top** 或 **limit** 所設定的值。

**First** 目前具有最大允許值 _5000_。

## <a name="skipping-records"></a>略過記錄

使用大型資料集的下一個選項是 **Skip** 控制項。 此控制項可讓查詢先跳過或略過所定義的記錄數目，然後再傳回結果。 **Skip** 適用於以有意義的方式排序結果的查詢，其目的是取得位於結果集中間某處的記錄。 如果所需的結果位於所傳回資料集的結尾處，則使用不同的排序設定，並改為從資料集頂端擷取結果會更有效率。

> [!NOTE]
> 使用**Skip**時，建議您至少使用一個具有 `asc` 或 `desc`的資料行來排序結果。 如果沒有排序，傳回的結果會是隨機的，而且無法重複。

下列範例示範如何略過查詢所產生的前 _10_ 筆記錄，改為從所傳回結果集的第 11 筆記錄開始：

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

在 [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) 中，此控制項為 **$skip** 且屬於 **QueryRequestOptions**。

## <a name="paging-results"></a>分頁結果

當您需要將結果集分割成較小的記錄集以進行處理，或因為結果集會超過允許的_1000_傳回記錄值上限，請使用分頁。 [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse**會提供值，以指出結果集已被分解： **resultTruncated**和 **$skipToken**。
**resultTruncated** 是一個布林值，如果回應中有其他未傳回的記錄，則會通知取用者。 在 **count** 屬性小於 **totalRecords** 屬性時，也可識別出此條件。 **totalRecords** 會定義符合查詢的記錄筆數。

當 **resultTruncated** 為 **true** 時，即會在回應中設定 **$skipToken** 屬性。 此值會與相同的查詢和訂用帳戶值搭配使用，以取得下一組符合查詢的記錄。

下列範例示範如何**略過**前3000筆記錄，並在 Azure CLI 和 Azure PowerShell 略過那些記錄之後傳回**前**1000 筆記錄：

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> 查詢必須**投影** **識別碼**欄位，才能使分頁運作。 如果查詢中遺漏了它，回應將不會包含 **$skipToken**。

如需範例，請參閱 REST API 文件中的[下一頁查詢](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query)。

## <a name="formatting-results"></a>格式化結果

Resource Graph 查詢的結果會以兩種格式（_資料表_和_ObjectArray_）提供。 格式是以**resultFormat**參數來設定，做為要求選項的一部分。 _資料表_格式是**resultFormat**的預設值。

預設會以 JSON 提供 Azure CLI 的結果。 Azure PowerShell 中的結果預設為**PSCustomObject** ，但使用 `ConvertTo-Json` Cmdlet 可以快速地將其轉換為 JSON。 針對其他 Sdk，可以將查詢結果設定為輸出_ObjectArray_格式。

### <a name="format---table"></a>格式資料表

預設的格式_Table_會以 JSON 格式傳回結果，其設計目的是要強調查詢所傳回之屬性的資料行設計和資料列值。 此格式與結構化資料表或試算表中所定義的資料非常類似，其中包含先識別的資料行，然後每個資料列都代表對齊這些資料行的資料。

以下是具有_資料表_格式的查詢結果範例：

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

### <a name="format---objectarray"></a>格式-ObjectArray

_ObjectArray_格式也會以 JSON 格式傳回結果。 不過，這項設計會與 JSON 中常見的索引鍵/值配對關聯性一致，其中資料行和資料列資料會在陣列群組中相符。

以下是具有_ObjectArray_格式之查詢結果的範例：

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

以下是將**resultFormat**設定為使用_ObjectArray_格式的一些範例：

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

- 請參閱[入門查詢](../samples/starter.md)中使用的語言。
- 請參閱 advanced[查詢](../samples/advanced.md)中的 advanced 使用。
- 深入了解如何[探索資源](explore-resources.md)。