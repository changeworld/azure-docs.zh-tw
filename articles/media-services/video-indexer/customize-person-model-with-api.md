---
title: 使用視頻索引子 API 自訂人員模型
titleSuffix: Azure Media Services
description: 瞭解如何使用視頻索引子 API 自訂人員模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127898"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>使用影片索引器 API 自訂人員模型

影片索引器支援針對視訊內容進行臉部偵測和名人辨識。 名人識別功能涵蓋大約 100 萬張基於通常請求的資料來源（如 IMDB、維琪百科和頂級LinkedIn影響者）的面部。 檢測到名人識別功能無法識別的面孔，但未命名。 將視頻上傳到視頻索引子並獲取結果後，您可以返回並命名未識別的人臉。 一旦您以姓名標示某個人臉之後，這張臉和姓名就會新增至您帳戶的人員模型中。 之後，影片索引器便能在未來和過去的視訊中辨識這張臉。

您可以使用影片索引器 API 編輯在視訊中偵測到的人臉，如本主題中所述。 您也可以使用影片索引器網站，如[使用影片索引器網站自訂人員模型](customize-person-model-with-api.md)中所述。

## <a name="managing-multiple-person-models"></a>管理多個人員模型

影片索引器針對每個帳戶皆支援多個人員模型。 此功能目前僅能透過影片索引器 API 使用。

如果您的帳戶可適用於不同的使用案例，您應該針對每個帳戶建立多個人員模型。 例如，如果您的內容與體育相關，則可以為每個運動（足球、籃球、足球等）創建單獨的 Person 模型。

一旦建立模型之後，您就可以在針對視訊進行上傳/編製索引或重新編製索引時，藉由提供特定人員模型的模型識別碼來使用該模型。 針對視訊進行新人臉的定型，可更新與該視訊相關聯的特定自訂模型。

每個帳戶都有 50 個人員模型的限制。 如果您不需要多人模型支援，則在上傳/索引或重新編制索引時，不要向視頻分配人員模型 ID。 在此情況下，影片索引器會使用您帳戶中的預設自訂人員模型。

## <a name="create-a-new-person-model"></a>建立新的人員模型

要在指定帳戶中創建新的人員模型，請使用[創建人員模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)API。

回應會依照下列範例的格式，提供您剛才建立之人員模型的名稱和產生的模型識別碼。

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

然後，在[將視頻上載到索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或[重新索引視頻](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)時，使用**personModelId**參數的**id**值。

## <a name="delete-a-person-model"></a>刪除人員模型

要從指定帳戶中刪除自訂人員模型，請使用[刪除人員模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)API。

一旦成功刪除人員模型之後，使用已刪除模型之目前視訊的索引會維持不變，直到您重新編製索引為止。 重新編制索引後，已刪除模型中命名的面不會被使用該模型編制索引的當前視頻中的視頻索引子識別，但仍會檢測到這些面。 您目前使用已刪除模型編製索引的視訊現在會使用您帳戶的預設人員模型。 如果已刪除模型中的人臉也以您帳戶的預設模型命名，則仍可繼續辨識視訊中的那些人臉。

成功刪除"人"模型時，沒有返回的內容。

## <a name="get-all-person-models"></a>取得所有人員模型

要獲取指定帳戶中的所有人員模型，請使用[獲取人員模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)API。

回應提供帳戶中的所有人員模型（包括指定帳戶中的預設人員模型）及其每個名稱和帳號的清單，遵循以下示例的格式。

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

在[將視頻上載到索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或[重新索引視頻](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)時，`personModelId`可以使用參數的`id`"人"模型的值來選擇要用於視頻的模型。

## <a name="update-a-face"></a>更新人臉

此命令可讓您以使用視訊識別碼和人臉識別碼的名稱，更新視訊中的人臉。 然後，此操作將更新視頻在上傳/索引或重新索引時與"人"模型關聯的人員模型。 如果未指派任何人員模型，它會更新帳戶的預設人員模型。

然後，系統會識別共用同一人員模型的其他當前視頻中同一面的匹配項。 您其他目前視訊中的臉部辨識可能需要一些時間才會生效，因為這是批次處理。

您可以更新影片索引器辨識為使用新名稱之名人的人臉。 您提供的新名稱將會優先於內建的名人辨識。

要更新人臉，請使用[更新視頻面](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)API。

名稱對於 Person 模型是唯一的，因此，如果在同一人物模型中為兩個不同的面`name`提供相同的參數值，視頻索引子將人臉與同一個人一起查看，並在重新索引視頻後將其收斂。

## <a name="next-steps"></a>後續步驟

[使用影片索引器網站自訂人員模型](customize-person-model-with-website.md)
