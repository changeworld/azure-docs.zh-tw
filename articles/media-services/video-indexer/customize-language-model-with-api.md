---
title: 使用視頻索引子 API 自訂語言模型
titlesuffix: Azure Media Services
description: 瞭解如何使用視頻索引子 API 自訂語言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127986"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>使用視頻索引子 API 自訂語言模型

影片索引器可讓您上傳適應文字 (也就是從您希望引擎適應其詞彙的網域中取得的文字) 來建立自訂語言模型，以自訂語音辨識。 一旦您將模型定型之後，也會辨識出現在調整文字中的新字。

如需自訂語言模型的詳細概觀和最佳作法，請參閱[使用影片索引器自訂語言模型](customize-language-model-overview.md)。

您可以使用影片索引器 API，在您的帳戶中建立和編輯自訂語言模型，如本主題中所述。 您也可以使用網站，如[使用影片索引器網站自訂語言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>建立語言模型

[創建語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?)API 在指定的帳號中創建新的自訂語言模型。 您可以在此呼叫中上傳語言模型的檔案。 或者，您可以在這裡建立語言模型，並稍後透過更新語言模型，上傳模型的檔案。

> [!NOTE]
> 您仍然必須使用模型已啟用的檔案為模型定型，以了解其檔案的內容。 下一節說明為語言定型的指示。

要上載要添加到語言模型的檔，除了提供上述所需參數的值外，還必須使用 FormData 在正文中上載檔。 執行此任務的方法有兩種：

* 鍵將是檔案名，值將是 txt 檔。
* 金鑰將是檔案名，值將是 txt 檔的 URL。

### <a name="response"></a>回應

回應提供新創建的語言模型上的中繼資料，以及以下示例 JSON 輸出的格式的每個模型檔的中繼資料：

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>將語言模型定型

[訓練語言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train)API 在指定帳戶中訓練自訂語言模型，其中的內容在語言模型中上載並啟用。

> [!NOTE]
> 您必須先建立語言模型，再上傳其檔案。 您可以在創建語言模型或更新語言模型時上載檔。

### <a name="response"></a>回應

回應提供新培訓的語言模型上的中繼資料，以及以下示例 JSON 輸出的格式的每個模型檔的中繼資料：

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

返回`id`的標識是用於區分語言模型的唯一 ID，同時`languageModelId`用於[將視頻上載到索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)和[重新索引視頻](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)API（也稱為`linguisticModelId`視頻索引子上載/重新索引 API）。

## <a name="delete-a-language-model"></a>刪除語言模型

[刪除語言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete)API 將從指定的帳號中刪除自訂語言模型。 使用已刪除的語言模型的任何視頻都將保留相同的索引，直到重新索引視頻。 如果重新對視頻進行索引，則可以為視頻分配新的語言模型。 否則，視頻索引子將使用其預設模型重新索引視頻。

### <a name="response"></a>回應

成功刪除語言模型時，沒有返回的內容。

## <a name="update-a-language-model"></a>更新語言模型

[更新語言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update)API 更新指定帳戶中的自訂語言人員模型。

> [!NOTE]
> 您必須已經建立語言模型。 您可以使用這個呼叫啟用或停用模式下的所有檔案、更新語言模型的名稱，以及上傳要新增至語言模型的檔案。

要上載要添加到語言模型的檔，除了提供上述所需參數的值外，還必須使用 FormData 在正文中上載檔。 執行此任務的方法有兩種：

* 鍵將是檔案名，值將是 txt 檔。
* 金鑰將是檔案名，值將是 txt 檔的 URL。

### <a name="response"></a>回應

回應提供新培訓的語言模型上的中繼資料，以及以下示例 JSON 輸出的格式的每個模型檔的中繼資料：

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

`id`使用回應中返回的檔下載檔案的內容。

## <a name="update-a-file-from-a-language-model"></a>從語言模型更新檔案

檔[的更新](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update)允許您更新指定帳戶中自訂語言模型中的檔`enable`的名稱和狀態。

### <a name="response"></a>回應

回應會針對您依照下列範例 JSON 輸出格式更新的檔案，提供中繼資料。

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

`id`使用回應中返回的檔下載檔案的內容。

## <a name="get-a-specific-language-model"></a>取得特定的語言模型

[get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API 返回指定帳戶中指定語言模型的資訊，如語言和語言模型中的檔。

### <a name="response"></a>回應

回應提供指定語言模型上的中繼資料，以及以下示例 JSON 輸出的格式的每個模型檔的中繼資料：

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

`id`使用回應中返回的檔下載檔案的內容。

## <a name="get-all-the-language-models"></a>取得所有語言模型

[獲取所有](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get)API 返回清單中指定帳戶中的所有自訂語言模型。

### <a name="response"></a>回應

回應提供帳戶中所有語言模型的清單及其每個中繼資料和檔，遵循此示例 JSON 輸出的格式：

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>從語言模型刪除檔案

[刪除](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete)API 從指定帳戶中的指定語言模型中刪除指定的檔。

### <a name="response"></a>回應

當檔從語言模型成功刪除時，沒有返回的內容。

## <a name="get-metadata-on-a-file-from-a-language-model"></a>從語言模型取得檔案的中繼資料

檔 API[的獲取中繼資料](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model)從帳戶中所選的語言模型返回指定檔的內容和中繼資料。

### <a name="response"></a>回應

回應以 JSON 格式提供檔的內容和中繼資料，類似于以下示例：

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> 此範例檔案的內容是 "hello" 和 "world" 在兩個不同的行。

## <a name="download-a-file-from-a-language-model"></a>從語言模型下載檔案

[下載檔案](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?)API 從指定帳戶中的指定語言模型中下載包含指定檔內容的文字檔。 此文字檔應符合原本上傳之文字檔的內容。

### <a name="response"></a>回應

回應將會是以 JSON 格式，下載具有檔案內容的文字檔。

## <a name="next-steps"></a>後續步驟

[使用網站自訂語言模型](customize-language-model-with-website.md)
