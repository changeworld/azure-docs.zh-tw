---
title: 使用影片索引子 API 自訂人員模型
titleSuffix: Azure Media Services
description: 瞭解如何使用影片索引子 API 自訂人員模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 09366dea1a0d77052b6f99e9f5ab52c270e341b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047022"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>使用影片索引器 API 自訂人員模型

影片索引器支援針對視訊內容進行臉部偵測和名人辨識。 名人辨識功能涵蓋關於以一般要求的資料來源（例如 IMDB、維琪百科和熱門 LinkedIn 影響因素）為基礎的1000000臉部。 偵測到名人辨識功能無法辨識的臉部，但會保留未命名。 將影片上傳至影片索引子並取回結果之後，您可以返回並命名無法辨識的臉部。 一旦您以姓名標示某個人臉之後，這張臉和姓名就會新增至您帳戶的人員模型中。 之後，影片索引器便能在未來和過去的視訊中辨識這張臉。

您可以使用影片索引器 API 編輯在視訊中偵測到的人臉，如本主題中所述。 您也可以使用影片索引器網站，如[使用影片索引器網站自訂人員模型](customize-person-model-with-api.md)中所述。

## <a name="managing-multiple-person-models"></a>管理多個人員模型

影片索引器針對每個帳戶皆支援多個人員模型。 此功能目前僅能透過影片索引器 API 使用。

如果您的帳戶可適用於不同的使用案例，您應該針對每個帳戶建立多個人員模型。 例如，如果您的內容與體育相關，您接著可以針對每個運動建立個別的人員模型 (足球、籃球、足球等) 。

一旦建立模型之後，您就可以在針對視訊進行上傳/編製索引或重新編製索引時，藉由提供特定人員模型的模型識別碼來使用該模型。 針對視訊進行新人臉的定型，可更新與該視訊相關聯的特定自訂模型。

每個帳戶都有 50 個人員模型的限制。 如果您不需要多個人員模型支援，請不要在上傳/編制索引或重新編制索引時，將人員模型識別碼指派給您的影片。 在此情況下，影片索引器會使用您帳戶中的預設自訂人員模型。

## <a name="create-a-new-person-model"></a>建立新的人員模型

若要在指定的帳號中建立新的人員模型，請使用 [建立人員模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) API。

回應會依照下列範例的格式，提供您剛才建立之人員模型的名稱和產生的模型識別碼。

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

然後，您可以在[上傳影片來為影片編制索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或重新[編制](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)索引時，使用**personModelId**參數的**識別碼**值。

## <a name="delete-a-person-model"></a>刪除人員模型

若要從指定的帳號刪除自訂人員模型，請使用 [ [刪除人員模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) API]。

一旦成功刪除人員模型之後，使用已刪除模型之目前視訊的索引會維持不變，直到您重新編製索引為止。 重新編制索引之後，目前影片中使用該模型編制索引的影片索引子將無法辨識已刪除之模型中所命名的臉部，但仍會偵測到臉部。 您目前使用已刪除模型編製索引的視訊現在會使用您帳戶的預設人員模型。 如果已刪除模型中的人臉也以您帳戶的預設模型命名，則仍可繼續辨識視訊中的那些人臉。

成功刪除人員模型時，不會傳回任何內容。

## <a name="get-all-person-models"></a>取得所有人員模型

若要取得指定帳戶中的所有人員模型，請使用 [取得人員模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) API。

回應會提供您帳戶中所有人員模型的清單 (包括指定帳戶中的預設人員模型) ，以及其每個名稱和識別碼遵循下列範例的格式。

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

您可以在 `id` `personModelId` [上傳影片來為影片編制索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) 或重新 [編制](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)索引時，選擇要用於影片的模型，方法是使用參數的人員模型值。

## <a name="update-a-face"></a>更新人臉

此命令可讓您以使用視訊識別碼和人臉識別碼的名稱，更新視訊中的人臉。 然後，此動作會在上傳/編制索引或重新編制索引時，更新與影片相關聯的人員模型。 如果未指派任何人員模型，它會更新帳戶的預設人員模型。

系統接著會辨識出您其他目前的影片中，共用相同人員模型的相同臉部。 您其他目前視訊中的臉部辨識可能需要一些時間才會生效，因為這是批次處理。

您可以更新影片索引器辨識為使用新名稱之名人的人臉。 您提供的新名稱將會優先於內建的名人辨識。

若要更新臉部，請使用 [更新影片臉部](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) API。

名稱是人員模型的唯一名稱，因此，如果您在相同人員模型中提供兩個不同的臉部給相同的 `name` 參數值，則影片索引子會將臉部視為相同的人員，並在您重新索引您的影片之後將其聚合。

## <a name="next-steps"></a>接下來的步驟

[使用影片索引器網站自訂人員模型](customize-person-model-with-website.md)
