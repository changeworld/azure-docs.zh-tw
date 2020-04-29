---
title: 如何指定辨識模型-臉部
titleSuffix: Azure Cognitive Services
description: 本文將說明如何選擇要與您的 Azure 臉部應用程式搭配使用的辨識模型。
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "76938833"
---
# <a name="specify-a-face-recognition-model"></a>指定臉部辨識模式

本指南說明如何使用 Azure 臉部服務指定臉部偵測、識別和相似性搜尋的臉部辨識模型。

臉部服務會使用機器學習模型，對影像中的人臉執行作業。 我們會根據客戶的意見反應和研究的進展，持續改善模型的精確度，並將這些改良功能當做模型更新來提供。 開發人員可以選擇指定他們想要使用的臉部辨識模型版本;他們可以選擇最適合其使用案例的模型。

如果您是新的使用者，建議使用最新的模型。 請繼續閱讀以瞭解如何在不同的臉部作業中指定它，同時避免模型衝突。 如果您是 advanced 使用者，而且不確定是否應該切換至最新的模型，請跳至[評估不同](#evaluate-different-models)的模型一節，以評估新的模型，並使用您目前的資料集來比較結果。

## <a name="prerequisites"></a>先決條件

您應該熟悉 AI 臉部偵測和識別的概念。 如果不是，請先參閱下列操作指南：

* [如何偵測影像中的臉部](HowtoDetectFacesinImage.md)
* [如何識別影像中的臉部](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>使用指定的模型偵測臉部

臉部偵測會識別人臉的視覺地標，並尋找其周框方塊位置。 它也會解壓縮臉部的功能，並加以儲存以供辨識。 這些資訊全都形成一個臉部的標記法。

當臉部功能已解壓縮時，會使用辨識模型，因此您可以在執行偵測作業時指定模型版本。

使用[臉部-]偵測 API 時，請使用`recognitionModel`參數指派模型版本。 可用的值為：

* `recognition_01`
* `recognition_02`

（選擇性）您可以指定_returnRecognitionModel_參數（預設**為 false**）來指出是否應該傳回_recognitionModel_以回應。 因此，[臉部]偵測 REST API 的要求 URL 看起來會像這樣：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

如果您使用用戶端程式庫，您可以藉`recognitionModel`由傳遞代表版本的字串來指派的值。
如果您將它保留為未指派，則會使用預設模型版本（_recognition_01_）。 請參閱下列 .NET 用戶端程式庫的程式碼範例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>使用指定的模型識別臉部

臉部服務可以從影像中取出臉部資料，並將它與**Person**物件產生關聯（例如，透過[新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API 呼叫），而多個**Person**物件可以一起儲存在**PersonGroup**中。 然後，可以將新的臉部與**PersonGroup** （使用[臉部識別]通話）進行比較，並且可以識別該群組內的相符人員。

**PersonGroup**應該有一個唯一的辨識模型供所有**人**使用，而且您可以在建立群組時`recognitionModel` （[PersonGroup-建立]或[LargePersonGroup-create]）使用參數來指定。 如果您未指定此參數，則會使用`recognition_01`原始模型。 群組一律會使用所建立的辨識模型，而且新的臉部會在新增至其中時，與此模型產生關聯;這無法在群組建立後變更。 若要查看**PersonGroup**的設定是哪一個模型，請使用[PersonGroup-Get] API 並將_returnRecognitionModel_參數設定為**true**。

請參閱下列 .NET 用戶端程式庫的程式碼範例。

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

在此程式碼中**PersonGroup** ，會建立`mypersongroupid`識別碼為的 PersonGroup，並設定為使用_recognition_02_模型來將臉部功能解壓縮。

同樣地，您必須指定偵測臉部以與此**PersonGroup** （透過[臉部-]偵測 API）比較時所要使用的模型。 您使用的模型應該一律與**PersonGroup**的設定一致;否則，作業將會因為模型不相容而失敗。

[臉部-識別]API 中沒有任何變更;您只需要指定偵測中的模型版本。

## <a name="find-similar-faces-with-specified-model"></a>使用指定的模型尋找相似的臉部

您也可以指定相似性搜尋的識別模型。 使用 FaceList 建立臉部清單`recognitionModel`時，您可以指派模型版本[-Create] API 或[LargeFaceList-create]。 如果您未指定此參數，則會使用`recognition_01`原始模型。 臉部清單一律會使用所建立的辨識模型，而新的臉部則會在新增至該模型時，與此模型產生關聯;這在建立之後就無法變更。 若要查看已設定臉部清單的模型，請使用[FaceList-Get] API，並將_returnRecognitionModel_參數設為**true**。

請參閱下列 .NET 用戶端程式庫的程式碼範例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

此程式碼會使用功能解壓縮`My face collection`的_recognition_02_模型，建立名為的臉部清單。 當您搜尋此臉部清單中是否有類似的臉部到新偵測到的臉部時，必須使用_recognition_02_模型偵測到該臉部（[臉部]偵測）。 如上一節所示，此模型必須一致。

[臉部-尋找類似]API 中沒有任何變更;您只會指定偵測中的模型版本。

## <a name="verify-faces-with-specified-model"></a>使用指定的模型驗證臉部

[臉部驗證]API 會檢查兩個臉部是否屬於同一個人。 除了辨識模型，驗證 API 中沒有任何變更，但是您只能比較使用相同模型偵測到的人臉。 因此，兩個臉部都必須使用`recognition_01`或`recognition_02`偵測到。

## <a name="evaluate-different-models"></a>評估不同的模型

如果您想要比較_recognition_01_的效能和_recognition_02_模型的資料，您將需要：

1. 分別建立具有_recognition_01_和_recognition_02_的兩個**PersonGroup**。
1. 使用您的影像資料來偵測臉部，並將其註冊到這兩個**PersonGroup**的**人員**，並使用[PersonGroup 訓練]API 觸發定型程式。
1. 使用臉部進行測試-在兩個**PersonGroup**上[識別]，並比較結果。

如果您通常會指定信賴閾值（介於零和一個的值，以決定模型必須如何辨識臉部），您可能需要針對不同的模型使用不同的閾值。 一個模型的臨界值不應該與另一個模型共用，也不一定會產生相同的結果。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何指定要搭配不同臉部服務 Api 使用的辨識模型。 接下來，請遵循快速入門，開始使用臉部偵測。

* [臉部 .NET SDK](../Quickstarts/csharp-sdk.md)
* [臉部 Python SDK](../Quickstarts/python-sdk.md)
* [臉部 Go SDK](../Quickstarts/go-sdk.md)

[臉部 - 偵測]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[臉部-尋找相似]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[臉部 - 識別]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[臉部-驗證]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - 定型]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList-建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
