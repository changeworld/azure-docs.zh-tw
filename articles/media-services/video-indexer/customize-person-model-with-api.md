---
title: 使用影片索引器 API 自訂人員模型 - Azure
titleSuffix: Azure Media Services
description: 本文說明如何使用影片索引器 API 自訂人員模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 370e9e515359e2e2e598db90aa379f796b13c3fe
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292394"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>使用影片索引器 API 自訂人員模型

影片索引器支援針對視訊內容進行臉部偵測和名人辨識。 名人辨識功能能根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，涵蓋大約 1 百萬個人臉。 名人辨識功能無法辨識的人臉仍會被偵測到，但不會被標註姓名。 將視訊上傳到影片索引器並取回結果之後，您可以返回為無法辨認的人臉命名。 一旦您以姓名標示某個人臉之後，這張臉和姓名就會新增至您帳戶的人員模型中。 之後，影片索引器便能在未來和過去的視訊中辨識這張臉。

您可以使用影片索引器 API 編輯在視訊中偵測到的人臉，如本主題中所述。 您也可以使用影片索引器網站，如[使用影片索引器網站自訂人員模型](customize-person-model-with-api.md)中所述。

## <a name="managing-multiple-person-models"></a>管理多個人員模型 

影片索引器針對每個帳戶皆支援多個人員模型。 此功能目前僅能透過影片索引器 API 使用。

如果您的帳戶可適用於不同的使用案例，您應該針對每個帳戶建立多個人員模型。 例如，如果您的內容與運動相關，則可以針對每種運動 (美式足球、籃球，足球等) 建立個別的人員模型。 

一旦建立模型之後，您就可以在針對視訊進行上傳/編製索引或重新編製索引時，藉由提供特定人員模型的模型識別碼來使用該模型。 針對視訊進行新人臉的定型，可更新與該視訊相關聯的特定自訂模型。

每個帳戶都有 50 個人員模型的限制。 如果您不需要多個人員模型支援，在上傳/編製索引或重新編製索引時，請勿將人員模型識別碼指派給您的視訊。 在此情況下，影片索引器會使用您帳戶中的預設自訂人員模型。

## <a name="create-a-new-person-model"></a>建立新的人員模型

若要在指定的帳號中建立新的人員模型，請使用[建立人員模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)API。

回應會依照下列範例的格式，提供您剛才建立之人員模型的名稱和產生的模型識別碼。

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

接著，您應該在[上傳要編製索引的視訊](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) \(英文\) 或[對視訊重新編製索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) \(英文\) 時，使用 **personModelId** 參數的 **id** 值。

## <a name="delete-a-person-model"></a>刪除人員模型

若要從指定的帳號中刪除自訂人員模型，請使用[刪除人員模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)API。 

一旦成功刪除人員模型之後，使用已刪除模型之目前視訊的索引會維持不變，直到您重新編製索引為止。 新編製索引時，影片索引器將無法在使用已刪除模型編製索引的目前視訊中，辨識在該模型中命名的人臉，但是仍然能夠偵測到那些人臉。 您目前使用已刪除模型編製索引的視訊現在會使用您帳戶的預設人員模型。 如果已刪除模型中的人臉也以您帳戶的預設模型命名，則仍可繼續辨識視訊中的那些人臉。

成功刪除人員模型時，沒有任何傳回的內容。

## <a name="get-all-person-models"></a>取得所有人員模型

若要取得指定帳戶中的所有人員模型，請使用[取得人員模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)API。

回應會依照以下範例的格式，提供您帳戶中所有人員模型 (包括指定之帳戶中的預設人員模型) 及各自名稱與識別碼的清單。

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

您可以在[將視訊上傳至索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或[重新編製視訊索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)時，針對 **personModelId** 參數使用人員模型的 **id** 值，藉此選擇您要用於視訊的模型。

## <a name="update-a-face"></a>更新人臉

此命令可讓您以使用視訊識別碼和人臉識別碼的名稱，更新視訊中的人臉。 之後，這會在上傳/編製索引或重新編製索引時，更新與視訊相關聯的人員模型。 如果未指派任何人員模型，它會更新帳戶的預設人員模型。 

一旦發生這種情況，它會辨識您其他目前視訊中，共用相同人員模型的相同人臉出現的次數。 您其他目前視訊中的臉部辨識可能需要一些時間才會生效，因為這是批次處理。

您可以更新影片索引器辨識為使用新名稱之名人的人臉。 您提供的新名稱將會優先於內建的名人辨識。

若要更新臉部，請使用[更新影片臉部](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)API。

名稱對於人員模型而言是獨一無二的，因此如果您在相同的人員模型中為兩個不同的人臉提供相同的 **name** 參數值，影片索引器會將這些人臉視為同一個人，並在您重新編製視訊索引時加以匯整。 

## <a name="next-steps"></a>後續步驟

[使用影片索引器網站自訂人員模型](customize-person-model-with-website.md)
