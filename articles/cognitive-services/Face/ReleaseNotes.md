---
title: 版本資訊 - 面容服務
titleSuffix: Azure Cognitive Services
description: Face 服務的版本資訊包括各種版本的發佈更改歷史記錄。
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165860"
---
# <a name="face-release-notes"></a>人臉版本資訊

本文涉及人臉服務版本 1.0。

### <a name="release-changes-in-june-2019"></a>2019 年 6 月發佈更改

* 添加了新的人臉檢測模型，提高了小面視圖、遮擋和模糊面的精度。 通過人臉[- 檢測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)，[面清單 - 添加面，](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)[大臉清單 - 添加面](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)，[人組人 - 添加人臉](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)- 添加人臉 -`detectionModel`通過指定新的[人臉](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)檢測模型名稱`detection_02`在參數。 [有關如何指定檢測模型](Face-API-How-to-Topics/specify-detection-model.md)的更多詳細資訊。

### <a name="release-changes-in-april-2019"></a>2019 年 4 月發佈更改

* 提高了 和`age``headPose`屬性的總體準確性。 屬性`headPose`也會更新，現在啟用的值`pitch`。 通過在[面 - 檢測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)`returnFaceAttributes``returnFaceAttributes`參數中指定這些屬性來使用這些屬性。 

* 提高人臉的速度[- 檢測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)，[面清單 - 添加面，](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)[大臉清單 - 添加面](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)，[人組人 - 添加面](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和[大型人物組人 - 添加面](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)。

### <a name="release-changes-in-march-2019"></a>2019 年 3 月發佈更改

* 添加了新的人臉識別模型，提高了準確性。 通過[人臉檢測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[面清單 - 創建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)、[大型面清單 - 創建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[人員組 - 創建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)和[大型人員組 -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)通過在參數中`recognition_02``recognitionModel`指定新的人臉識別模型名稱來創建。 [有關如何指定識別模型](Face-API-How-to-Topics/specify-recognition-model.md)的更多詳細資訊。

### <a name="release-changes-in-january-2019"></a>2019 年 1 月的發行變更

* 添加了快照功能以支援跨訂閱的資料移轉：[快照](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get)。 [有關如何將人臉資料移轉到其他面容訂閱](Face-API-How-to-Topics/how-to-migrate-face-data.md)中的更多詳細資訊。

### <a name="release-changes-in-october-2018"></a>2018 年 10 月的發行變更

* [PersonGroup - 取得訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)、[LargePersonGroup - 取得訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) 及 [LargeFaceList - 取得訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf) 中 `status`、`createdDateTime`、`lastActionDateTime` 和 `lastSuccessfulTrainingDateTime` 的精簡描述。

### <a name="release-changes-in-may-2018"></a>2018 年 5 月的發行變更

* 已大幅改善 `gender` 屬性，並已改善 `age`、`glasses`、`facialHair`、`hair`、`makeup` 屬性。 您可以透過 [Face - Detect (臉部 - 偵測) 的 ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 參數來使用這些屬性。 

* 在 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測)、[FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - 新增臉部)、[LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) (LargeFaceList - 新增臉部)、[PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - 新增臉部) 和 [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) (LargePersonGroup Person - 新增臉部) 中，輸入影像檔大小限制從 4 MB 提高到 6 MB。

### <a name="release-changes-in-march-2018"></a>2018 年 3 月的發行變更

* 新增百萬規模容器：[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) 和 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)。 如需詳細資料，請參閱[如何使用大規模功能](Face-API-How-to-Topics/how-to-use-large-scale.md)。

* 將 [Face - Identify (臉部 - 識別) 的 ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` 參數從 [1, 5] 提高到 [1, 100]，預設為 10。

### <a name="release-changes-in-may-2017"></a>2017 年 5 月的發行變更

* 在 [Face - Detect (臉部 - 偵測) 的 ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 參數中新增 `hair`、`makeup`、`accessory`、`occlusion`、`blur`、`exposure` 和 `noise` 屬性。

* 在 PersonGroup 和 [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (臉部 - 識別) 中支援 一萬人。

* [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) (PersonGroup Person - 列出) 支援分頁，其選擇性參數為：`start` 和 `top`。

* 支援同時對不同的 FaceList 和 PersonGroup 中的不同人員新增/刪除臉部。

### <a name="release-changes-in-march-2017"></a>2017 年 3 月的發行變更
* 在 [Face - Detect (臉部 - 偵測) 的 ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 參數中新增 `emotion` 屬性。

* 修正無法使用從 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測) 傳回的矩形，重新偵測臉部作為 [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - 新增臉部) 和 [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - 新增臉部) 中的 `targetFace`。

* 修正可偵測的臉部大小，確保它完全介於 36x36 到 4096x4096 像素之間。

### <a name="release-changes-in-november-2016"></a>2016 年 11 月的發行變更
* 新增臉部儲存體標準訂用帳戶，在使用 [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - 新增臉部) 或 [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - 新增臉部) 時，持續儲存更多的臉部，供比對識別及相似度之用。 儲存影像的計費方式為每 1000 張人臉美金 $0.5，並會依使用天數按比例計算。 免費層訂用帳戶的總人數會繼續僅限 1,000 名。

### <a name="release-changes-in-october-2016"></a>2016 年 10 月的發行變更
* 在 [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - 新增臉部) 和 [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - 新增臉部) 中，將 targetFace 中有多個臉部的錯誤訊息，從 'There are more than one face in the image' 變更為 'There is more than one face in the image'。

### <a name="release-changes-in-july-2016"></a>2016 年 7 月的發行變更
* 在 [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (臉部 - 驗證) 中支援 Face 對 Person 物件的驗證。

* 在 [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (臉部 - 尋找類似項目) 中新增選擇性 `mode` 參數，讓您可以選取兩個工作模式：`matchPerson` 和 `matchFace`，預設為 `matchPerson`。

* 在 [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (臉部 - 識別) 中新增選擇性 `confidenceThreshold` 參數，讓使用者可以設定某個臉部是否屬於 Person 物件的閾值。

* 在 [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) (PersonGroup - 列出) 中新增選擇性 `start` 和 `top` 參數，讓使用者可以為清單指定起點和 PersonGroup 總數。

### <a name="v10-changes-from-v0"></a>從 V0 變更為 V1.0
* 將服務根端點從 ```https://westus.api.cognitive.microsoft.com/face/v0/``` 更新為 ```https://westus.api.cognitive.microsoft.com/face/v1.0/```。 變更適用於：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測)、[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (臉部 - 識別)、[Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (臉部 - 尋找類似項目) 和 [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (臉部 - 分組)。

* 將可偵測的臉部大小下限更新為 36x36 像素。 小於 36x36 像素的臉部將無法被偵測。

* 臉部 V0 中的 PersonGroup 和 Person 資料已淘汰。 無法使用臉部 V1.0 服務存取這些資料。

* 臉部 API 的 V0 端點已於 2016 年 6 月 30 日淘汰。
