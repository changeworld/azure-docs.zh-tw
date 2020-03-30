---
title: 使用視頻索引子 API 自訂品牌模型
titleSuffix: Azure Media Services
description: 瞭解如何使用視頻索引子 API 自訂品牌模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127999"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>使用影片索引器 API 自訂品牌模型

影片索引器支援在視訊和音訊內容的編製索引及重新編製索引期間，從語音和視覺文字偵測品牌。 品牌偵測功能可識別 Bing 的品牌資料庫建議所提及的產品、服務以及公司。 例如，如果視頻或音訊內容中提到 Microsoft，或者在視頻中以可視文本顯示，視頻索引子會將其檢測為內容中的品牌。 自訂品牌模型可讓您從偵測到的品牌中排除特定品牌，並包含應該是您模型的一部分，但可能不在 Bing 品牌資料庫中的品牌。

如需詳細概觀，請參閱[概觀](customize-brands-model-overview.md)。

您可以使用影片索引器 API 建立、使用和編輯在視訊中偵測到的自訂品牌模型，如本主題中所述。 您也可以使用影片索引器網站，如[使用影片索引器網站自訂品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="create-a-brand"></a>建立品牌

[創建品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand)API 創建新的自訂品牌，並將其添加到指定帳戶的自訂品牌模型中。

> [!NOTE]
> 將`enabled`（在正文中）設置為 true 將品牌置於"*包含*"清單中，以便視頻索引子進行檢測。 設置為`enabled`false 將品牌置於 *"排除"* 清單中，因此視頻索引子無法檢測到它。

可以在正文中設置的一些其他參數：

* 該`referenceUrl`值可以是該品牌的任何參考網站，例如指向其維琪百科頁面的連結。
* 該`tags`值是品牌的標籤清單。 此標記顯示在視頻索引子網站上的品牌*類別*欄位中。 例如，您可以將品牌 "Azure" 標記或分類為「雲端」。

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

[刪除品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?)API 會從指定帳戶的自訂品牌模型中刪除品牌。 該帳戶在`accountId`參數中指定。 一旦呼叫成功之後，該品牌將不再位於 [包含]** 或 [排除]** 品牌清單中。

### <a name="response"></a>回應

成功刪除品牌時，不會返回任何內容。

## <a name="get-a-specific-brand"></a>取得特定品牌

[獲取品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?)API 允許您使用品牌 ID 搜索自訂品牌模型中品牌的詳細資訊。

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
> `enabled`設置為`true`表示品牌位於"視頻索引子要檢測的*包含"* 清單中，並且`enabled`false 表示品牌位於 *"排除"* 清單中，因此視頻索引子不會檢測到它。

## <a name="update-a-specific-brand"></a>更新特定品牌

[通過更新品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?)API，您可以使用品牌 ID 搜索自訂品牌模型中品牌的詳細資訊。

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

[獲取所有品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?)API 返回指定帳戶的自訂品牌模型中的所有品牌，無論該品牌是要在 *"包含"* 還是 *"排除*品牌"清單中。

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
> 名為 *"示例"* 的品牌位於"視頻索引子要檢測的*包含"* 清單中，而名為*示例 2*的品牌位於 *"排除"* 清單中，因此視頻索引子不會檢測到它。

## <a name="get-brands-model-settings"></a>取得品牌模型設定

[獲取品牌設置](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands)API 返回指定帳戶中的品牌模型設置。 品牌模型設定表示是否已從 Bing 品牌資料庫中啟用偵測。 如果未啟用必應品牌，視頻索引子將僅檢測指定帳戶的自訂品牌模型中的品牌。

### <a name="response"></a>回應

回應會顯示是否依照下列範例的格式啟用 Bing 品牌。

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`設置為 true 表示必應品牌已啟用。 如果`useBuiltin`為 false，則禁用必應品牌。 可以`state`忽略該值，因為它已被棄用。

## <a name="update-brands-model-settings"></a>更新品牌模型設定

[更新品牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?)API 更新指定帳戶中的品牌模型設置。 品牌模型設定表示是否已從 Bing 品牌資料庫中啟用偵測。 如果未啟用必應品牌，視頻索引子將僅檢測指定帳戶的自訂品牌模型中的品牌。

設置為`useBuiltIn`true 的標誌表示必應品牌已啟用。 如果`useBuiltin`為 false，則禁用必應品牌。

### <a name="response"></a>回應

成功更新品牌模型設置時，沒有返回的內容。

## <a name="next-steps"></a>後續步驟

[使用網站自訂品牌模型](customize-brands-model-with-website.md)
