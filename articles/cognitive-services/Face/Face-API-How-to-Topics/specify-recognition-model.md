---
title: 如何指定辨識模型-臉部
titleSuffix: Azure Cognitive Services
description: 本文將說明如何選擇要搭配 Azure 臉部應用程式使用的辨識模型。
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: 016b8bf010f597e963e0901d1ec48486f79bbb35
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913121"
---
# <a name="specify-a-face-recognition-model"></a>指定臉部辨識模式

本指南說明如何使用 Azure 臉部辨識服務來指定臉部偵測、識別和相似性搜尋的臉部辨識模型。

臉部辨識服務會使用機器學習模型，對影像中的人臉執行作業。 我們會根據客戶的意見反應和研究的進展，持續改善模型的精確度，而且我們會將這些改進提供為模型更新。 開發人員可以選擇指定想要使用的臉部辨識模型版本;他們可以選擇最適合其使用案例的模型。

Azure 臉部辨識服務有三個可用的辨識模型。 模型 _recognition_01_ (發佈的 2017) 和 _recognition_02_ (發行的 2019) ，以確保使用以這些模型建立之 facelist 或 **PersonGroup** 的客戶回溯相容性。 **FaceList** 或 **Persongroup** 一律會使用它所建立的辨識模型，而新的臉部將會在新增時與此模型產生關聯。 在建立之後，就無法變更，客戶必須使用對應的辨識模型與對應的 **FaceList** 或 **PersonGroup** 。

您可以輕鬆地移至稍後的辨識模型;不過，您將需要使用您選擇的辨識模型來建立新的 Facelist 和 Persongroup。

_Recognition_03_ 模型 (發行的 2020) 是目前可用的最精確模型。 如果您是新客戶，我們建議使用此模型。 _Recognition_03_ 會針對相似性比較和人員比對比較提供改良的精確度。 請注意，每個模型彼此獨立運作，而針對某個模型設定的信賴閾值則不會在其他辨識模型之間進行比較。

請繼續閱讀以瞭解如何在不同的臉部作業中指定選取的模型，同時避免模型衝突。 如果您是 advanced 使用者，而且想要判斷是否應該切換至最新的模型，請跳至 [ [評估不同](#evaluate-different-models) 的模型] 區段，以評估新的模型，並使用您目前的資料集來比較結果。


## <a name="prerequisites"></a>Prerequisites

您應該熟悉 AI 臉部偵測和識別的概念。 如果不是，請先參閱下列指南：

* [臉部偵測概念](../concepts/face-detection.md)
* [臉部辨識概念](../concepts/face-recognition.md)
* [如何偵測影像中的臉部](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>使用指定的模型偵測臉部

臉部偵測會識別人臉的視覺地標，並尋找其周框方塊的位置。 它也會將臉部的功能解壓縮，並加以儲存以供識別。 所有這項資訊都形成一種臉部的標記法。

在將臉部特徵解壓縮時，會使用辨識模型，因此您可以在執行偵測作業時指定模型版本。

使用 [臉部] 偵測 API 時，請使用參數指派模型版本 `recognitionModel` 。 可用的值為：
* recognition_01
* recognition_02
* recognition_03


您可以選擇性地指定 _returnRecognitionModel_ 參數 (預設值 **為 false** ) ，指出是否應在回應中傳回 _recognitionModel_ 。 因此， [臉部] 偵測 REST API 的要求 URL 看起來會像這樣：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

如果您使用用戶端程式庫，您可以傳遞代表版本的字串來指派的值 `recognitionModel` 。 如果您將它保留為未指派，則會使用的預設模型版本 `recognition_01` 。 請參閱下列 .NET 用戶端程式庫的程式碼範例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>使用指定的模型識別臉部

臉部辨識服務可以從影像中提取臉部資料，並將其與 **Person** 物件 (透過「 [新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API」呼叫（例如) ），也可以將多個 **Person** 物件儲存在 **PersonGroup** 中。 然後，您可以使用 [臉部辨識]通話) 來比較 **PersonGroup** (的新臉部，並識別該群組內的相符人員。

**PersonGroup** 的所有 **人員** 都應該有一個唯一的辨識模型，而且您可以在建立群組時使用參數指定此模型 `recognitionModel` ( [PersonGroup]建立或 [LargePersonGroup 建立]) 。 如果您未指定此參數，則 `recognition_01` 會使用原始模型。 群組一律會使用它所建立的辨識模型，而且新的臉部會在新增時與此模型產生關聯;這無法在群組建立之後變更。 若要查看設定 **PersonGroup** 的模型，請使用 [PersonGroup-Get] API 並將 _returnRecognitionModel_ 參數設定為 **true** 。

請參閱下列 .NET 用戶端程式庫的程式碼範例。

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

在此程式碼中，會建立具有識別碼的 **PersonGroup** `mypersongroupid` ，並將其設定為使用 _recognition_02_ 模型來將臉部特徵解壓縮。

同樣地，您必須指定在偵測臉部時要使用哪一個模型，以透過 [臉部]偵測 API) 來與這個 **PersonGroup** (進行比較。 您使用的模型應該一律與 **PersonGroup** 的設定一致;否則，作業將會因為不相容的模型而失敗。

[臉部辨識]API 沒有任何變更;您只需要指定偵測中的模型版本。

## <a name="find-similar-faces-with-specified-model"></a>使用指定的模型尋找相似臉部

您也可以指定相似性搜尋的辨識模型。 `recognitionModel`使用[FaceList]建立 API 或[LargeFaceList]建立時，您可以在建立臉部清單時指派模型版本。 如果您未指定此參數，則 `recognition_01` 預設會使用此模型。 臉部清單一律會使用建立它的辨識模型，而且新的臉部會在新增至清單時與此模型產生關聯;您無法在建立之後變更此變更。 若要查看已設定臉部清單的模型，請使用 [FaceList-Get] API 並將 _returnRecognitionModel_ 參數設為 **true** 。

請參閱下列 .NET 用戶端程式庫的程式碼範例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");
```

此程式碼會 `My face collection` 使用功能解壓縮的 _recognition_03_ 模型，來建立名為的臉部清單。 當您在此臉部清單中搜尋與新偵測到臉部相似的臉部時，必須偵測到臉部 ( [臉部] 偵測) 使用 _recognition_03_ 模型。 如上一節所示，模型必須是一致的。

[臉部-尋找類似]的 API 沒有任何變更;您只會指定偵測中的模型版本。

## <a name="verify-faces-with-specified-model"></a>使用指定的模型驗證臉部

[臉部驗證]API 會檢查兩個臉部是否屬於同一個人。 對辨識模型而言，驗證 API 沒有任何變更，但您只能比較使用相同模型偵測到的臉部。

## <a name="evaluate-different-models"></a>評估不同的模型

如果您想要比較不同辨識模型在您自己的資料上的效能，您將需要：
1. 分別使用 _recognition_01_ 、 _recognition_02_ 和 _recognition_03_ 建立三個 persongroup。
1. 使用您的影像資料來偵測臉部，並將其註冊到這三個 **PersonGroup** 中的 **人員** 。 
1. 使用 PersonGroup-訓練 API 來訓練您的 Persongroup。
1. 在所有三個 **PersonGroup** 上測試臉部辨識，並比較結果。


如果您通常會指定信賴臨界值 (介於零之間的值，以及決定模型必須如何才能識別臉部) 的值，則可能需要針對不同的模型使用不同的臨界值。 一個模型的臨界值不會與另一個模型共用，而且不一定會產生相同的結果。

## <a name="next-steps"></a>下一步

在本文中，您已瞭解如何指定要搭配不同臉部服務 Api 使用的辨識模型。 接下來，請遵循快速入門以開始使用臉部偵測。

* [臉部 .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [臉部 Python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [臉部 Go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[臉部 - 偵測]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[臉部-尋找相似之處]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[臉部 - 識別]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[臉部-驗證]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList-建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc