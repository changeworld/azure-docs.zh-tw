---
title: 使用大型資料集
description: 了解如何在使用 Azure Resource Graph 時取得、格式化、分頁及略過大型資料集中的記錄。
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: 4b45a28a5dbd2ebc233bcf9a6808cb7d7cd6d8c8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681063"
---
# <a name="working-with-large-azure-resource-data-sets"></a>使用大型 Azure 資源資料集

Azure Resource Graph 是設計來使用和取得 Azure 環境中資源的相關資訊。 即使在查詢數千筆記錄時，Resource Graph 還是能夠快速取得此資料。 Resource Graph 有數個選項可用來使用這些大型資料集。

如需以較高頻率處理查詢的指引，請參閱[節流要求指引](./guidance-for-throttled-requests.md)。

## <a name="data-set-result-size"></a>資料集結果大小

根據預設，Resource Graph 會將所有查詢限制為只傳回 **100** 筆記錄。 此控制項可保護使用者和服務，以防止會產生大型資料集的非預期查詢。 此事件大多會在客戶利用符合其特定需求的方式來實驗查詢以尋找並篩選資源時發生。 此控制項不同於使用 [top](/azure/kusto/query/topoperator) 或 [limit](/azure/kusto/query/limitoperator) Azure 資料總管語言運算子來限制結果。

> [!NOTE]
> 使用 **第一個**時，建議至少依照一個具有 `asc` 或 `desc` 的資料行來排序結果。 若未排序，傳回的結果會是隨機的且不可重複。

預設限制可以透過與 Resource Graph 互動的所有方法來覆寫。 下列範例示範如何將資料集大小限制變更為 _200_：

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

在 [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) 中，此控制項為 **$top** 且屬於 **QueryRequestOptions**。

「限制最嚴格」的控制項將會勝出。 例如，如果您的查詢會使用 **top** 或 **limit** 運算子並產生比 **First** 還多的記錄，則傳回的記錄數目上限會等於 **First**。 同樣地，如果 **top** 或 **limit** 小於 **First**，傳回的記錄集會是小於 **top** 或 **limit** 所設定的值。

**First** 目前允許的最大值為 _5000_，藉由一次將結果 _1000_ 筆記錄[分頁](#paging-results)即可達成。

> [!IMPORTANT]
> 當 **First** 設定為大於 _1000_ 筆記錄時，查詢必須**推算** **id** 欄位，以便讓分頁正常運作。 如果查詢中遺漏了 First，回應將不會進行[分頁](#paging-results)，且結果受限於 _1000_ 筆記錄。

## <a name="skipping-records"></a>略過記錄

使用大型資料集的下一個選項是 **Skip** 控制項。 此控制項可讓查詢先跳過或略過所定義的記錄數目，然後再傳回結果。 **Skip** 適用於以有意義的方式排序結果的查詢，其目的是取得位於結果集中間某處的記錄。 如果所需的結果位於所傳回資料集的結尾處，則使用不同的排序設定，並改為從資料集頂端擷取結果會更有效率。

> [!NOTE]
> 使用 **Skip**時，建議至少依照一個具有 `asc` 或 `desc` 的資料行來排序結果。 若未排序，傳回的結果會是隨機的且不可重複。

下列範例示範如何略過查詢所產生的前 _10_ 筆記錄，改為從所傳回結果集的第 11 筆記錄開始：

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

在 [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) 中，此控制項為 **$skip** 且屬於 **QueryRequestOptions**。

## <a name="paging-results"></a>分頁結果

如果需要將結果集分解為較小的記錄集以進行處理，或者因為結果集可能超過 _1000_ 筆傳回記錄的允許值上限，請使用分頁。 [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse** 會提供值來表示已將結果集分解：**resultTruncated** 和 **$skipToken**。
**resultTruncated** 是一個布林值，如果回應中有其他未傳回的記錄，則會通知取用者。 在 **count** 屬性小於 **totalRecords** 屬性時，也可識別出此條件。 **totalRecords** 會定義符合查詢的記錄筆數。

 當分頁已停用或因為沒有 `id` 資料行而無法進行時，或當可用的資源少於查詢所要求的資源時，則 **resultTruncated** 為 **true**。 當 **resultTruncated** 為 **true** 時，則不會設定 **$skipToken** 屬性。

下列範例示範如何**略過**前 3000 筆記錄，並在使用 Azure CLI 和 Azure PowerShell 略過這些記錄之後，傳回 **前** 1000 筆記錄：

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> 查詢必須**投影** **識別碼**欄位，才能使分頁運作。 如果查詢中遺漏了 Skip，回應將不會包含 **$skipToken**。

如需範例，請參閱 REST API 文件中的[下一頁查詢](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query)。

## <a name="formatting-results"></a>格式化結果

Resource Graph 查詢的結果會以兩種格式提供：_Table_ 和 _ObjectArray_。 格式會使用 **resultFormat** 參數來設定為要求選項的一部分。 _Table_ 格式是 **resultFormat** 的預設值。

預設會以 JSON 提供來自 Azure CLI 的結果。 Azure PowerShell 中的結果預設為 **PSCustomObject**，但可以使用 `ConvertTo-Json` Cmdlet 快速地轉換為 JSON。 對於其他 SDK，可以將查詢結果設定為輸出 _的 ObjectArray_ 格式。

### <a name="format---table"></a>格式 - Table

預設格式 _Table_會以 JSON 格式傳回結果，其設計訴求是要醒目提示查詢所傳回屬性的資料行設計和資料列值。 此格式與結構化資料表或試算表中定義的資料非常類似，其中先列出已識別的資料行，接著是代表與這些資料行一致之資料的每個資料列。

以下是 _Table_ 格式的查詢結果範例：

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

### <a name="format---objectarray"></a>格式 - ObjectArray

_ObjectArray_ 格式也會以 JSON 格式傳回結果。 不過，此設計會與 JSON 中常見的索引鍵/值組關聯性保持一致，其中資料行和資料列資料會在陣列群組中相符。

以下是 _ObjectArray_ 格式的查詢結果範例：

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

以下是一些將 **resultFormat** 設定為使用 _ObjectArray_ 格式的範例：

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
- 請參閱[進階查詢](../samples/advanced.md)中的進階使用方式。
- 深入了解如何[探索資源](explore-resources.md)。