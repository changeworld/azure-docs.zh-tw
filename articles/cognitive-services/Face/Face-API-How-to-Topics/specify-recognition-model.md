---
title: 如何指定識別模型 - 面
titleSuffix: Azure Cognitive Services
description: 本文將介紹如何選擇與 Azure 面應用程式一起使用的識別模型。
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938833"
---
# <a name="specify-a-face-recognition-model"></a>指定臉部辨識模式

本指南演示如何使用 Azure 人臉服務為人臉檢測、識別和相似性搜索指定人臉識別模型。

人臉服務使用機器學習模型在圖像中對人臉執行操作。 我們根據客戶回函和研究進展，不斷提高模型的準確性，並將這些改進作為模型更新提供。 開發人員可以選擇指定要使用的人臉識別模型的版本;他們可以選擇最適合其用例的模型。

如果您是新使用者，我們建議您使用最新的型號。 請繼續閱讀，瞭解如何在不同的面操作中指定它，同時避免模型衝突。 如果您是高級使用者，但不確定是否應切換到最新模型，請跳到["評估不同模型](#evaluate-different-models)"部分以評估新模型並使用當前資料集比較結果。

## <a name="prerequisites"></a>Prerequisites

您應該熟悉 AI 人臉檢測和識別的概念。 如果不是，請參閱以下如何指導：

* [如何檢測圖像中的人臉](HowtoDetectFacesinImage.md)
* [如何識別圖像中的人臉](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>檢測具有指定型號的面

人臉檢測可識別人臉的視覺地標，並查找其邊界框位置。 它還提取面部特徵並存儲它們以進行識別。 所有這些資訊都構成一個面的表示形式。

提取人臉特徵時使用識別模型，以便在執行檢測操作時指定模型版本。

使用面[- 檢測]API 時，使用`recognitionModel`參數分配模型版本。 可用的值為：

* `recognition_01`
* `recognition_02`

或者，您可以指定_返回識別模型_參數（預設**false），** 以指示是否應在回應中返回_識別模型_。 因此，人臉的請求 URL [- 檢測]REST API 如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

如果使用用戶端庫，可以通過傳遞表示版本的字串來分配 的值`recognitionModel`。
如果未分配，將使用預設模型版本 _（recognition_01_）。 請參閱以下代碼示例，瞭解 .NET 用戶端庫。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>使用指定型號識別面

人臉服務可以從圖像中提取人臉資料並將其與**Person**物件關聯（例如，通過[添加人臉](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API 呼叫），並且多個**Person**物件可以一起存儲在**PersonGroup**中。 然後，可以將新面孔與**PersonGroup**進行比較（帶有[人臉 - 識別]呼叫），並可以識別該組中的匹配人員。

**PersonGroup**應具有所有**人員**的唯一識別模型，並且您可以在創建組時使用`recognitionModel`參數指定該模型（[人員組 - 創建]或[大型人員組 - 創建]）。 如果不指定此參數，則使用原始`recognition_01`模型。 組將始終使用其創建的識別模型，並且新面在添加到該模型時將與此模型相關聯;在組創建後無法更改此項。 要查看**人組**配置了什麼模型，請使用["人組-獲取]API"，返回_識別模型_參數設置為**true**。

請參閱以下代碼示例，瞭解 .NET 用戶端庫。

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

在此代碼中，將創建具有 ID `mypersongroupid` **的 PersonGroup，** 並將其設置為使用_recognition_02_模型提取人臉要素。

相應地，您需要指定在檢測要與此**PersonGroup**進行比較的面時要使用的模型（通過人臉 -[檢測]API）。 您使用的模型應始終與**PersonGroup**的配置一致;否則，操作將因模型不相容而失敗。

面 -[識別]API 中沒有任何更改;您只需在檢測中指定模型版本。

## <a name="find-similar-faces-with-specified-model"></a>使用指定型號查找類似的面

您還可以為相似性搜索指定識別模型。 使用[面清單]創建人臉清單`recognitionModel`時，可以使用 "創建"面清單 - 創建 API 或[大型面表 - 創建]"創建 "，使用 分配模型版本。 如果不指定此參數，則使用原始`recognition_01`模型。 面清單將始終使用其創建的識別模型，並且新面在添加到該模型時將與此模型相關聯;這在創建後無法更改。 要查看人臉清單配置了什麼模型，請使用[FaceList - 獲取]API，返回_識別模型_參數設置為**true**。

請參閱以下代碼示例，瞭解 .NET 用戶端庫。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

此代碼使用_recognition_02_模型創建名為`My face collection`的面清單，用於特徵提取。 當您搜索此面清單以查找與新檢測到的人臉相似的面時，必須使用_recognition_02_模型檢測到該面（[人臉 - 檢測]）。 如上一節所述，模型需要一致。

面沒有變化[- 查找類似的]API;您只在檢測中指定模型版本。

## <a name="verify-faces-with-specified-model"></a>使用指定型號驗證面

[人臉 - 驗證]API 檢查兩個面是否屬於同一個人。 驗證 API 中沒有關于識別模型的更改，但只能比較使用同一模型檢測到的面。 因此，兩個面都需要使用`recognition_01`或`recognition_02`檢測到。

## <a name="evaluate-different-models"></a>評估不同的模型

如果您想比較_recognition_01_的性能，並在資料上_recognition_02_模型，您需要：

1. 分別使用_recognition_01_和_recognition_02_創建兩**個人員組**。
1. 使用圖像資料檢測人臉並將其註冊到這兩**個 PersonGroup**的**Person**s，並使用[PersonGroup - 培訓]API 觸發培訓過程。
1. 使用人臉測試 - 在兩**個人員組**[上識別]並比較結果。

如果通常指定置信度閾值（介於零和 0 之間的值，該值確定模型識別面的置信度），則可能需要對不同的模型使用不同的閾值。 一個模型的閾值不意味著要共用到另一個模型，也不一定會產生相同的結果。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何指定用於不同面服務 API 的識別模型。 接下來，按照快速入門開始使用人臉檢測。

* [面 .NET SDK](../Quickstarts/csharp-sdk.md)
* [人臉 Python SDK](../Quickstarts/python-sdk.md)
* [人臉去 SDK](../Quickstarts/go-sdk.md)

[臉部 - 偵測]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[臉 - 查找類似]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[臉部 - 識別]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[人臉 - 驗證]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[人員組 - 獲取]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - 定型]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[面清單 - 創建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[面清單 - 獲取]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[大面清單 - 創建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
