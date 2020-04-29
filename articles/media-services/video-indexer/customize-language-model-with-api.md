---
title: 使用影片索引子 API 自訂語言模型
titlesuffix: Azure Media Services
description: 瞭解如何使用影片索引子 API 自訂語言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127986"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>使用影片索引子 API 自訂語言模型

影片索引器可讓您上傳適應文字 (也就是從您希望引擎適應其詞彙的網域中取得的文字) 來建立自訂語言模型，以自訂語音辨識。 一旦您將模型定型之後，也會辨識出現在調整文字中的新字。

如需自訂語言模型的詳細概觀和最佳作法，請參閱[使用影片索引器自訂語言模型](customize-language-model-overview.md)。

您可以使用影片索引器 API，在您的帳戶中建立和編輯自訂語言模型，如本主題中所述。 您也可以使用網站，如[使用影片索引器網站自訂語言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>建立語言模型

[建立語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?)API 會在指定的帳號中建立新的自訂語言模型。 您可以在此呼叫中上傳語言模型的檔案。 或者，您可以在這裡建立語言模型，並稍後透過更新語言模型，上傳模型的檔案。

> [!NOTE]
> 您仍然必須使用模型已啟用的檔案為模型定型，以了解其檔案的內容。 下一節說明為語言定型的指示。

若要上傳要新增至語言模型的檔案，除了提供上述必要參數的值之外，您還必須使用 FormData 上傳主體中的檔案。 有兩種方式可以執行這項工作：

* 金鑰會是檔案名，而值則是 txt 檔案。
* 金鑰會是檔案名，而值則是 txt 檔案的 URL。

### <a name="response"></a>回應

回應會依照下列範例 JSON 輸出的格式，提供新建立之語言模型的中繼資料，以及每個模型檔案的中繼資料：

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

[訓練語言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train)API 會在指定的帳號中訓練自訂語言模型，其中包含已上傳至語言模型並在其中啟用的檔案內容。

> [!NOTE]
> 您必須先建立語言模型，再上傳其檔案。 建立語言模型或更新語言模型時，您可以上傳檔案。

### <a name="response"></a>回應

回應會依照下列範例 JSON 輸出的格式，提供新定型之語言模型的中繼資料，以及每個模型檔案的中繼資料：

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

`id`傳回的`languageModelId`是用來區別語言模型的唯一識別碼，而則是用來[上傳影片來編制索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)並重新[編制影片](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)api （也稱為`linguisticModelId`在影片索引子上傳/重新編制 api）。

## <a name="delete-a-language-model"></a>刪除語言模型

[刪除語言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete)API 會從指定的帳號中刪除自訂語言模型。 任何使用已刪除之語言模型的影片都會保留相同的索引，直到您重新編制影片索引為止。 如果您將影片重新編制索引，您可以將新的語言模型指派給影片。 否則，影片索引子會使用其預設模型來重新編制影片索引。

### <a name="response"></a>回應

成功刪除語言模型時，沒有任何傳回的內容。

## <a name="update-a-language-model"></a>更新語言模型

[更新語言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update)API 會更新指定帳戶中的自訂語言人員模型。

> [!NOTE]
> 您必須已經建立語言模型。 您可以使用這個呼叫啟用或停用模式下的所有檔案、更新語言模型的名稱，以及上傳要新增至語言模型的檔案。

若要上傳要新增至語言模型的檔案，除了提供上述必要參數的值之外，您還必須使用 FormData 上傳主體中的檔案。 有兩種方式可以執行這項工作：

* 金鑰會是檔案名，而值則是 txt 檔案。
* 金鑰會是檔案名，而值則是 txt 檔案的 URL。

### <a name="response"></a>回應

回應會依照下列範例 JSON 輸出的格式，提供新定型之語言模型的中繼資料，以及每個模型檔案的中繼資料：

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

`id`使用回應中所傳回檔案的來下載檔案的內容。

## <a name="update-a-file-from-a-language-model"></a>從語言模型更新檔案

[[更新](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update)檔案] 可讓您在指定的帳號`enable`中，更新自訂語言模型中檔案的名稱和狀態。

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

使用回應`id`中所傳回之檔案的來下載檔案的內容。

## <a name="get-a-specific-language-model"></a>取得特定的語言模型

[Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API 會傳回指定之帳戶中指定語言模型的相關資訊，例如語言和語言模型中的檔案。

### <a name="response"></a>回應

回應會依照下列範例 JSON 輸出的格式，提供指定之語言模型的中繼資料，以及每個模型檔案的中繼資料：

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

使用回應`id`中所傳回之檔案的來下載檔案的內容。

## <a name="get-all-the-language-models"></a>取得所有語言模型

「[取得所有](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get)API」會傳回清單中指定之帳戶內的所有自訂語言模型。

### <a name="response"></a>回應

回應會以下列範例 JSON 輸出的格式，提供您帳戶中所有語言模型的清單，以及其每個中繼資料和檔案：

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

[Delete](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) API 會從指定之帳戶中指定的語言模型刪除指定的檔案。

### <a name="response"></a>回應

成功從語言模型中刪除檔案時，沒有任何傳回的內容。

## <a name="get-metadata-on-a-file-from-a-language-model"></a>從語言模型取得檔案的中繼資料

檔案 API 的[取得中繼資料](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model)會從您帳戶中所選的語言模型，傳回指定檔案的和中繼資料內容。

### <a name="response"></a>回應

回應會以 JSON 格式提供檔案的內容和中繼資料，與下列範例類似：

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

[下載](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?)檔案 API 會從指定之帳戶中指定的語言模型，下載包含指定檔案內容的文字檔。 此文字檔應符合原本上傳之文字檔的內容。

### <a name="response"></a>回應

回應將會是以 JSON 格式，下載具有檔案內容的文字檔。

## <a name="next-steps"></a>後續步驟

[使用網站自訂語言模型](customize-language-model-with-website.md)
