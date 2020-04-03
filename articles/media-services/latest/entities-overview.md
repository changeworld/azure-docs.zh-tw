---
title: 媒體服務實體的篩選、排序和分頁
titleSuffix: Azure Media Services
description: 瞭解 Azure 媒體服務 v3 實體的篩選、排序和分頁。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7e4f1141a9d4bd58451782e8412063a22565556d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584524"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>媒體服務實體的篩選、排序和分頁

本主題討論列出 Azure 媒體服務 v3 實體時可用的 OData 查詢選項和分頁支援。

## <a name="considerations"></a>考量

* `Datetime`類型實體的屬性始終採用UTC格式。
* 在發送請求之前,查詢字串中的空白應對 URL 進行編碼。

## <a name="comparison-operators"></a>比較運算子

可以使用以下運算元將欄位與常量值進行比較:

相等運算子:

- `eq`:測試欄位是否*等於*常量值。
- `ne`:測試欄位*是否不等於*常量值。

範圍運算子:

- `gt`:測試欄位是否*大於*常量值。
- `lt`:測試欄位是否*小於*常量值。
- `ge`: 測試欄位是否*大於或等於*常量值。
- `le`: 測試欄位是否*小於或等於*常量值。

## <a name="filter"></a>Filter

提供`$filter`OData 篩選器參數,以便僅尋找您感興趣的物件。

以下 REST 範例`alternateId`篩選資產的價值:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

以下 C# 範例篩選資產建立日期:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>按

用於`$orderby`按指定的參數對返回的物件進行排序。 例如：  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

要按升序或降序對結果進行排序,請追加欄位`asc``desc`名稱 ,由空格分隔。 例如： `$orderby properties/created desc` 。

## <a name="skip-token"></a>略過權杖

如果查詢回應包含許多項,則服務將返回用於獲取`$skiptoken``@odata.nextLink`下 一頁結果的 ( ) 值。 使用它來翻頁整個結果集。

在媒體服務 v3 中,無法配置頁面大小。 頁面大小因實體類型而異。 閱讀以下各個部分瞭解詳細資訊。

如果在通過集合分頁時創建或刪除實體,則更改將反映在返回的結果中(如果這些更改位於尚未下載的集合部分)。

> [!TIP]
> 始終用於`nextLink`枚舉集合,並且不依賴於特定的頁面大小。
>
> 僅當`nextLink`實體有多個頁時,該值才會存在。

請考慮以下使用位置`$skiptoken`的示例。 請務必將 amstestaccount** 取代為您的帳戶名稱，並將 api-version** 值設為最新版本。

如果您要求這樣的資產清單:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

你會得到類似於這個的回應:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

然後您會藉由傳送 get 要求來要求下一個頁面：

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

以下 C# 範例展示如何透過帳戶中的所有流式定位器枚舉。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>使用邏輯運算子組合查詢選項

媒體服務 v3 支援**OR** **和 AND**邏輯運算符。 

以下 REST 範例檢查工作的狀態:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

在 C# 中建構相同的查詢,如下所示: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>實體的篩選與排序選項

下表顯示了如何將篩選和排序選項應用於不同的實體:

|實體名稱|屬性名稱|Filter|單|
|---|---|---|---|
|[Assets](https://docs.microsoft.com/rest/api/media/assets/)|NAME|`eq`, `gt`, `lt`, `ge`, `le`|`asc` 和 `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` 和 `desc`|
|[內容關鍵原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|NAME|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.policyId|`eq`, `ne`||
|[工作](https://docs.microsoft.com/rest/api/media/jobs)| NAME  | `eq`            | `asc` 和 `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` 和 `desc`| 
|[串流式處理器](https://docs.microsoft.com/rest/api/media/streaminglocators)|NAME|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` 和 `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
|[流式處理原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)|NAME|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
|[轉換](https://docs.microsoft.com/rest/api/media/transforms)| NAME | `eq`            | `asc` 和 `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|

## <a name="next-steps"></a>後續步驟

* [列出資產](https://docs.microsoft.com/rest/api/media/assets/list)
* [列出內容金鑰原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [列出工作](https://docs.microsoft.com/rest/api/media/jobs/list)
* [列出流式處理原則](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [清單串流式處理器](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [串流處理檔案](stream-files-dotnet-quickstart.md)
* [配額和限制](limits-quotas-constraints.md)
