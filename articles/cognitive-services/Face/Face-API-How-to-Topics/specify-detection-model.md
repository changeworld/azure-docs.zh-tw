---
title: 如何指定檢測模型 - 面
titleSuffix: Azure Cognitive Services
description: 本文將介紹如何選擇與 Azure Face 應用程式一起使用的人臉檢測模型。
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934567"
---
# <a name="specify-a-face-detection-model"></a>指定臉部偵測模式

本指南演示如何為 Azure 人臉服務指定人臉檢測模型。

人臉服務使用機器學習模型在圖像中對人臉執行操作。 我們根據客戶回函和研究進展，不斷提高模型的準確性，並將這些改進作為模型更新提供。 開發人員可以選擇指定要使用的人臉檢測模型的版本;他們可以選擇最適合其用例的模型。

請繼續閱讀，瞭解如何在某些人臉操作中指定人臉檢測模型。 當人臉服務將人臉的圖像轉換為其他形式的資料時，它就會使用人臉檢測。

如果您不確定是否應使用最新模型，請跳到["評估不同模型](#evaluate-different-models)"部分以評估新模型並使用當前資料集比較結果。

## <a name="prerequisites"></a>Prerequisites

您應該熟悉 AI 人臉檢測的概念。 否則，請參閱人臉檢測概念指南或如何指南：

* [人臉檢測概念](../concepts/face-detection.md)
* [如何檢測圖像中的人臉](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>檢測具有指定型號的面

人臉檢測可查找人臉的邊界框位置，並標識其視覺地標。 它提取人臉的特徵，並將其存儲到以後用於[識別](../concepts/face-recognition.md)操作。

使用[面 - 檢測]API 時，可以使用 參數`detectionModel`分配模型版本。 可用的值為：

* `detection_01`
* `detection_02`

人臉的請求 URL [- 檢測]REST API 如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

如果使用用戶端庫，則可以通過傳入適當的字串來分配 的值`detectionModel`。 如果未分配，API 將使用預設模型版本 （）。`detection_01` 請參閱以下代碼示例，瞭解 .NET 用戶端庫。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>將面添加到具有指定型號的人員

人臉服務可以從圖像中提取人臉資料，並通過["人組人員 - 添加人臉](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)"API 將其與**人**物件關聯。 在此 API 呼叫中，可以指定檢測模型的方式與[在人臉檢測]中相同。

請參閱以下代碼示例，瞭解 .NET 用戶端庫。

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

此代碼創建具有 ID `mypersongroupid` **的人員組**，並將**人員**添加到該組。 然後 **，它使用**`detection_02`模型向此人添加面。 如果不指定*檢測模型*參數，API 將使用預設模型`detection_01`。

> [!NOTE]
> 不需要對**Person**物件中的所有面使用相同的檢測模型，並且在檢測新面以與**Person**物件進行比較時不需要使用相同的檢測模型（例如，在["人臉 - 識別]API"中）。

## <a name="add-face-to-facelist-with-specified-model"></a>使用指定型號將面添加到面清單

在將面添加到現有**FaceList**物件時，還可以指定檢測模型。 請參閱以下代碼示例，瞭解 .NET 用戶端庫。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

此代碼創建一個稱為`My face collection` **FaceList 的面**表，並在`detection_02`模型中向其添加面。 如果不指定*檢測模型*參數，API 將使用預設模型`detection_01`。

> [!NOTE]
> 不需要對**FaceList**物件中的所有面使用相同的檢測模型，並且在檢測新面以與**FaceList**物件進行比較時，也不需要使用相同的檢測模型。

## <a name="evaluate-different-models"></a>評估不同的模型

不同的人臉檢測模型針對不同的任務進行了優化。 有關差異的概述，請參閱下表。

|**detection_01**  |**detection_02**  |
|---------|---------|
|所有人臉檢測操作的預設選擇。 | 于 2019 年 5 月發佈，可在所有人臉檢測操作中可選。
|未針對小面、側視圖或模糊面進行優化。  | 提高了小面視圖和模糊面的精度。 |
|如果檢測調用中指定了人臉屬性（頭部姿勢、年齡、情緒等）。 |  不返回面屬性。     |
|如果在檢測調用中指定了這些地標，則返回這些地標。   | 不返回面地標。  |

比較`detection_01`和`detection_02`模型的性能的最佳方法是在示例資料集上使用它們。 我們建議使用每個檢測模型在各種圖像上調用[人臉檢測]API，尤其是許多人臉或難以看到的面孔的圖像。 注意每個模型返回的面數。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何指定要用於不同面 API 的檢測模型。 接下來，按照快速入門開始使用人臉檢測。

* [面 .NET SDK](../Quickstarts/csharp-sdk.md)
* [人臉 Python SDK](../Quickstarts/python-sdk.md)
* [人臉去 SDK](../Quickstarts/go-sdk.md)

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
