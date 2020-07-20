---
title: 臉部辨識概念
titleSuffix: Azure Cognitive Services
description: 本文說明驗證的概念、尋找類似的群組，以及識別臉部辨識作業和基礎資料結構。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73743069"
---
# <a name="face-recognition-concepts"></a>臉部辨識概念

本文說明驗證的概念、尋找類似的群組，以及識別臉部辨識作業和基礎資料結構。 大致上，辨識會描述比較兩個不同臉部的工作，以判斷它們是否類似或屬於同一人。

## <a name="recognition-related-data-structures"></a>辨識相關的資料結構

辨識作業主要使用下列資料結構。 這些物件會儲存在雲端中，並可由其識別碼字串加以參考。 識別碼字串在訂用帳戶內一律是唯一的。 名稱欄位可以重複。

|Name|描述|
|:--|:--|
|DetectedFace| [臉部偵測](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)作業會抓取這個單一臉部標記法。 其識別碼會在建立後的24小時後到期。|
|PersistedFace| 將 DetectedFace 物件新增至群組（例如 FaceList 或 Person）時，它們會變成 PersistedFace 物件。 它們[可以隨時取得](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)，而且不會過期。|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)或[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 此資料結構是 PersistedFace 物件的各種清單。 FaceList 有唯一的識別碼、名稱字串，以及選擇性的使用者資料字串。|
|[個人](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 此資料結構是屬於同一個人的 PersistedFace 物件清單。 它具有唯一的識別碼、名稱字串，以及選擇性的使用者資料字串。|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)或[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 此資料結構是 Person 物件的各種清單。 它具有唯一的識別碼、名稱字串，以及選擇性的使用者資料字串。 PersonGroup 必須[經過訓練](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)後，才能用於辨識作業。|

## <a name="recognition-operations"></a>辨識作業

本節將詳細說明四個辨識作業如何使用先前所述的資料結構。 如需每個辨識作業的廣泛說明，請參閱[總覽](../Overview.md)。

### <a name="verify"></a>Verify

[驗證](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)作業會接受來自 DetectedFace 或 PersistedFace 的臉部識別碼，以及另一個臉部識別碼或 Person 物件，並判斷它們是否屬於同一人。 如果您傳入 Person 物件，您可以選擇性地傳入該人員所屬的 PersonGroup 來改善效能。

### <a name="find-similar"></a>尋找類似項目

[尋找類似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)的作業會接受來自 DetectedFace 或 PersistedFace 的臉部識別碼，以及 FaceList 或其他臉部識別碼的陣列。 有了 FaceList，它會傳回類似于指定臉部的較小 FaceList。 使用臉部識別碼陣列時，會類似地傳回較小的陣列。

### <a name="group"></a>群組

[群組](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)作業會接受來自 DetectedFace 或 PersistedFace 的各種臉部識別碼陣列，並傳回群組成數個較小陣列的相同識別碼。 每個「群組」陣列都包含看起來類似的臉部識別碼。 單一「messyGroup」陣列包含的臉部識別碼，找不到任何相似處。

### <a name="identify"></a>識別

[識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)作業接受來自 DetectedFace 或 PersistedFace 的一個或多個臉部識別碼，以及一個 PersonGroup 並傳回每個臉部可能屬於的 Person 物件清單。 傳回的 Person 物件會包裝為候選物件，其具有預測信賴值。

## <a name="input-data"></a>輸入資料

請使用下列秘訣來確保您的輸入影像能提供最精確的辨識結果：

* 支援的輸入影像格式包括 JPEG、PNG、GIF （第一個畫面）、BMP。
* 影像檔案大小不應超過 4 MB。
* 當您建立 Person 物件時，請使用功能不同角度和光源的相片。
* 某些臉部可能因技術挑戰而無法辨識，例如：
  * 具有極端光源的影像，例如，嚴重的背光。
  * 封鎖一或兩個眼睛的障礙物。
  * 頭髮類型或臉部線的差異。
  * 臉部外觀的變更，因為存在時間。
  * 極端臉部運算式。

## <a name="next-steps"></a>後續步驟

既然您已熟悉臉部辨識概念，請瞭解如何撰寫腳本，以根據定型的 PersonGroup 來識別臉部。

* [識別影像中的臉部](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)