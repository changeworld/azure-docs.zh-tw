---
title: 臉部辨識概念
titleSuffix: Azure Cognitive Services
description: 本文介紹了驗證、查找相似、組和識別人臉識別操作和基礎資料結構的概念。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743069"
---
# <a name="face-recognition-concepts"></a>臉部辨識概念

本文介紹了驗證、查找相似、組和識別人臉識別操作和基礎資料結構的概念。 廣義地說，識別描述了比較兩個不同的面孔以確定它們是否相似或屬於同一個人的工作。

## <a name="recognition-related-data-structures"></a>與識別相關的資料結構

識別操作主要使用以下資料結構。 這些物件存儲在雲中，並且可以通過它們的 ID 字串引用。 ID 字串在訂閱中始終是唯一的。 可以複製名稱欄位。

|名稱|描述|
|:--|:--|
|DetectedFace| 此單面表示由[人臉檢測](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)操作檢索。 其 ID 在創建後 24 小時過期。|
|PersistedFace| 當檢測到的 Face 物件添加到組（如面表或人員）時，它們將成為持久面對象。 可以隨時[檢索](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)它們，並且不會過期。|
|[面清單](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)或[大面清單](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 此資料結構是持久化Face 物件的一系列清單。 FaceList 具有唯一 ID、名稱字串和可選的使用者資料字串。|
|[人](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 此資料結構是屬於同一人的持久化Face物件的清單。 它有一個唯一的 ID、一個名稱字串，並且可選有一個使用者資料字串。|
|[人組](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)或[大型人物組](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 此資料結構是 Person 物件的一系列清單。 它有一個唯一的 ID、一個名稱字串，並且可選有一個使用者資料字串。 在識別操作中使用 PersonGroup 之前，必須對人組[進行培訓](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)。|

## <a name="recognition-operations"></a>識別操作

本節詳細介紹了四個識別操作如何使用前面描述的資料結構。 有關每個識別操作的廣泛說明，請參閱[概述](../Overview.md)。

### <a name="verify"></a>Verify

["驗證](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)"操作獲取來自"已檢測到面"或"持久面"的人臉 ID，以及另一個人臉 ID 或"人"物件，並確定它們是否屬於同一個人。 如果傳遞人員物件，可以選擇將該人員所屬的人員組傳遞給該人員以提高性能。

### <a name="find-similar"></a>尋找類似項目

["查找類似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)"操作獲取來自"已檢測到面"或"持久面"的人臉 ID，以及面清單或其他人臉 ID 的陣列。 使用面表，它返回與給定面相似的較小面表。 使用面指示的陣列時，它同樣會返回較小的陣列。

### <a name="group"></a>群組

[組](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)操作從檢測到的面或持久面獲取各種面 ED 陣列，並返回分組到多個較小陣列中的相同 ED。 每個"組"陣列包含看起來相似的面 ID。 單個"messyGroup"陣列包含未找到相似性的面 ID。

### <a name="identify"></a>識別

["標識](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)"操作從檢測到的"已面"或"持久面"和"人組"獲取一個或多個人臉標識，並返回每個面可能所屬的人員物件清單。 返回的人員物件包裝為具有預測置信度值的候選物件。

## <a name="input-data"></a>輸入資料

使用以下提示確保輸入圖像提供最準確的識別結果：

* 支援的輸入圖像格式為 JPEG、PNG、GIF（第一幀）、BMP。
* 圖像檔案大小不應大於 4 MB。
* 創建"人物"物件時，請使用具有不同角度和照明特徵的照片。
* 由於技術挑戰，某些面孔可能無法識別，例如：
  * 具有極端照明的圖像，例如，嚴重背光。
  * 阻擋一隻眼睛或兩隻眼睛的障礙物。
  * 頭髮類型或面部毛髮的差異。
  * 由於年齡的變化，面部外觀發生了變化。
  * 極端的面部表情。

## <a name="next-steps"></a>後續步驟

現在，您已經熟悉人臉識別概念，瞭解如何編寫腳本，根據經過培訓的 PersonGroup 識別人臉。

* [識別影像中的臉部](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)