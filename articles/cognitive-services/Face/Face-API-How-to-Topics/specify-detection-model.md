---
title: 如何指定偵測模型-臉部
titleSuffix: Azure Cognitive Services
description: 本文將說明如何選擇要與您的 Azure 臉部應用程式搭配使用的臉部偵測模型。
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: a90b37b197e25a8db79a87761d94dfded53acf50
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323215"
---
# <a name="specify-a-face-detection-model"></a>指定臉部偵測模式

本指南說明如何為 Azure 臉部服務指定臉部偵測模型。

臉部服務會使用機器學習模型，對影像中的人臉執行作業。 我們會根據客戶的意見反應和研究的進展，持續改善模型的精確度，並將這些改良功能當做模型更新來提供。 開發人員可以選擇指定他們想要使用的臉部偵測模型版本;他們可以選擇最適合其使用案例的模型。

請繼續閱讀以瞭解如何在某些臉部作業中指定臉部偵測模型。 臉部服務會在將臉部影像轉換成某種其他形式的資料時，使用臉部偵測。

如果您不確定是否應該使用最新的模型，請跳至[評估不同](#evaluate-different-models)的模型一節，以評估新的模型，並使用您目前的資料集來比較結果。

## <a name="prerequisites"></a>必要條件

您應該熟悉 AI 臉部偵測的概念。 如果您沒有，請參閱臉部偵測概念指南或操作指南：

* [臉部偵測概念](../concepts/face-detection.md)
* [如何偵測影像中的臉部](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>使用指定的模型偵測臉部

臉部偵測會尋找人臉的周框方塊位置，並識別其視覺效果地標。 它會解壓縮臉部的功能並加以儲存，以供日後[辨識作業使用](../concepts/face-recognition.md)。

當您使用[臉部-]偵測 API 時，您可以使用參數指派模型版本 `detectionModel` 。 可用的值為：

* `detection_01`
* `detection_02`

[臉部]偵測 REST API 的要求 URL 如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

如果您使用用戶端程式庫，您可以藉由傳入適當的字串來指派的值 `detectionModel` 。 如果您將它保留為未指派，則 API 會使用預設模型版本（ `detection_01` ）。 請參閱下列 .NET 用戶端程式庫的程式碼範例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_03", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>使用指定的模型將臉部新增至人員

臉部服務可以從影像中取出臉部資料，並透過[PersonGroup person-新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API 將它與**Person**物件產生關聯。 在此 API 呼叫中，您可以用與[臉部-]偵測中相同的方式來指定偵測模型。

請參閱下列 .NET 用戶端程式庫的程式碼範例。

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_03");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

此程式碼會建立識別碼為的**PersonGroup** `mypersongroupid` ，並在其中加入**人員**。 然後，它會使用模型將臉部新增至這個**人員** `detection_02` 。 如果您未指定*detectionModel*參數，則 API 會使用預設模型 `detection_01` 。

> [!NOTE]
> 您不需要針對**Person**物件中的所有臉部使用相同的偵測模型，而且在偵測新臉部以與**person**物件（例如[臉部-識別]API）進行比較時，您不需要使用相同的偵測模型。

## <a name="add-face-to-facelist-with-specified-model"></a>使用指定的模型將臉部新增至 FaceList

您也可以在將臉部新增至現有的**FaceList**物件時，指定偵測模型。 請參閱下列 .NET 用戶端程式庫的程式碼範例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

此程式碼會建立名為的**FaceList** `My face collection` ，並使用模型將臉部加入其中 `detection_02` 。 如果您未指定*detectionModel*參數，則 API 會使用預設模型 `detection_01` 。

> [!NOTE]
> 您不需要針對**FaceList**物件中的所有臉部使用相同的偵測模型，而且在偵測新臉部以與**FaceList**物件比較時，您不需要使用相同的偵測模型。

## <a name="evaluate-different-models"></a>評估不同的模型

不同的臉部偵測模型會針對不同的工作進行優化。 如需差異的總覽，請參閱下表。

|**detection_01**  |**detection_02**  |
|---------|---------|
|所有臉部偵測作業的預設選項。 | 于5月2019發行，並可在所有臉部偵測作業中選擇性地使用。
|未針對小型、側視圖或模糊臉部進行優化。  | 改善小型、並排和模糊臉部的精確度。 |
|如果在偵測呼叫中指定了臉部屬性（head 姿勢、年齡、表情等等），則會傳回。 |  不會傳回臉部屬性。     |
|如果在偵測呼叫中指定臉部地標，則會傳回。   | 不會傳回臉部地標。  |

比較和模型效能的最佳方式 `detection_01` `detection_02` ，就是在範例資料集上使用它們。 我們建議您在各種影像上呼叫[臉部-]偵測 API，特別是許多臉部的影像，或使用每個偵測模型很容易看到的臉部。 請注意每個模型所傳回的臉部數目。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何指定要搭配不同臉部 Api 使用的偵測模型。 接下來，請遵循快速入門，開始使用臉部偵測。

* [臉部 .NET SDK](../Quickstarts/csharp-sdk.md)
* [臉部 Python SDK](../Quickstarts/python-sdk.md)
* [臉部 Go SDK](../Quickstarts/go-sdk.md)

[臉部 - 偵測]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[臉部 - 識別]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
