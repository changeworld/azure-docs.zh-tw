---
title: 使用影片索引子 API 自訂品牌模型
titleSuffix: Azure Media Services
description: 瞭解如何使用影片索引子 API 自訂品牌模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 5fc565ecc1b501f52e934784695594dcfef2a83a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047293"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>使用影片索引器 API 自訂品牌模型

影片索引器支援在視訊和音訊內容的編製索引及重新編製索引期間，從語音和視覺文字偵測品牌。 品牌偵測功能可識別 Bing 的品牌資料庫建議所提及的產品、服務以及公司。 例如，如果在影片或音訊內容中提及 Microsoft，或影片中出現了視覺效果文字，則影片索引子會將其偵測為內容中的品牌。 自訂品牌模型可讓您從偵測到的品牌中排除特定品牌，並包含應該是您模型的一部分，但可能不在 Bing 品牌資料庫中的品牌。

如需詳細概觀，請參閱[概觀](customize-brands-model-overview.md)。

您可以使用影片索引器 API 建立、使用和編輯在視訊中偵測到的自訂品牌模型，如本主題中所述。 您也可以使用影片索引器網站，如[使用影片索引器網站自訂品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="create-a-brand"></a>建立品牌

[建立品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand)API 會建立新的自訂品牌，並將其新增至指定帳戶的自訂品牌模型。

> [!NOTE]
> 將 `enabled` （在本文中）設定為 true 會將品牌放在影片索引子的 [*包含*] 清單中，以進行偵測。 `enabled`若設定為 false，則會將品牌放在*排除*清單中，因此影片索引子將不會偵測到它。

您可以在本文中設定的一些其他參數：

* 此 `referenceUrl` 值可以是品牌的任何參考網站，例如其維琪百科頁面的連結。
* 此 `tags` 值是品牌標記的清單。 此標記會顯示在影片索引子網站的品牌*類別*欄位中。 例如，您可以將品牌 "Azure" 標記或分類為「雲端」。

### <a name="response"></a>回應

回應會針對您剛才依照下列範例格式建立的品牌，提供相關資訊。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>刪除品牌

「[刪除品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?)API」會從指定之帳戶的自訂品牌模型中移除品牌。 此帳戶是在參數中指定 `accountId` 。 一旦呼叫成功之後，該品牌將不再位於 [包含]** 或 [排除]** 品牌清單中。

### <a name="response"></a>回應

成功刪除品牌時，沒有任何傳回的內容。

## <a name="get-a-specific-brand"></a>取得特定品牌

[取得品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?)API 可讓您使用品牌識別符，在指定帳戶的自訂品牌模型中搜尋品牌的詳細資料。

### <a name="response"></a>回應

回應會針對您剛才依照下列範例格式搜尋 (使用品牌識別碼) 的品牌，提供相關資訊。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled`設定為 `true` 表示品牌位於影片索引子要偵測的*Include*清單中，而 `enabled` False 表示品牌位於*排除*清單中，因此影片索引子將不會偵測到它。

## <a name="update-a-specific-brand"></a>更新特定品牌

[更新品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?)API 可讓您使用品牌識別符，在指定帳戶的自訂品牌模型中搜尋品牌的詳細資料。

### <a name="response"></a>回應

回應會針對您剛才依照下列範例格式更新的品牌，提供更新的資訊。

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>取得所有品牌

不論品牌是否要位於 [*包含*或*排除*品牌] 清單中，「[取得所有品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?)」 API 都會針對指定的帳號傳回自訂品牌模型中的所有品牌。

### <a name="response"></a>回應

回應會針對您帳戶中的所有品牌，依照下列範例的格式，提供一份清單及各自的詳細資料。

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> 名為*範例*的品牌位於要偵測之影片索引子的 [*包含*] 清單中，而名為*Example2*的品牌位於 [*排除*] 清單中，因此影片索引子將不會偵測到它。

## <a name="get-brands-model-settings"></a>取得品牌模型設定

[取得品牌設定](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands)API 會傳回指定帳戶中的品牌模型設定。 品牌模型設定表示是否已從 Bing 品牌資料庫中啟用偵測。 如果未啟用 Bing 品牌，影片索引子將只會從指定之帳戶的自訂品牌模型中偵測品牌。

### <a name="response"></a>回應

回應會顯示是否依照下列範例的格式啟用 Bing 品牌。

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`設定為 true 時，表示已啟用 Bing 品牌。 如果 `useBuiltin` 為 false，則會停用 Bing 品牌。 `state`可以忽略此值，因為它已被取代。

## <a name="update-brands-model-settings"></a>更新品牌模型設定

[更新品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?)API 會更新指定帳戶中的品牌模型設定。 品牌模型設定表示是否已從 Bing 品牌資料庫中啟用偵測。 如果未啟用 Bing 品牌，影片索引子將只會從指定之帳戶的自訂品牌模型中偵測品牌。

`useBuiltIn`旗標設為 true 表示已啟用 Bing 品牌。 如果 `useBuiltin` 為 false，則會停用 Bing 品牌。

### <a name="response"></a>回應

當品牌模型設定更新成功時，沒有任何傳回的內容。

## <a name="next-steps"></a>後續步驟

[使用網站自訂品牌模型](customize-brands-model-with-website.md)
