---
title: 如何將專案更新為 3.0 API
titleSuffix: Azure Cognitive Services
description: 瞭解如何將自訂視覺專案從以前的 API 版本更新為 3.0 API。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647509"
---
# <a name="update-to-the-30-api"></a>更新至 3.0 API

自訂願景現已達到通用性，並經歷了 API 更新。
此更新包括一些新功能，更重要的是，一些重大更改：

* 預測 API 現在根據專案類型分為兩個。
* 願景 AI 開發人員工具組 （VAIDK） 匯出選項要求以特定方式創建專案。
* 預設反覆運算已被刪除，有利於發佈/取消發佈命名反覆運算。

本指南將介紹如何更新專案以使用新的 API 版本。 有關更改的完整清單，請參閱[版本資訊](release-notes.md)。

## <a name="use-the-updated-prediction-api"></a>使用更新的預測 API

2.x API 對圖像分類器和物件檢測器專案使用相同的預測調用。 預測**圖像**和**預測圖像Url**調用可以接受這兩種專案類型。 從 3.0 開始，我們拆分此 API，以便您需要將專案類型與調用匹配：

* 使用**["分類圖像](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)**"和**["分類圖像Url"](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** 獲取圖像分類專案的預測。
* 使用**[檢測圖像](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** 和**[檢測圖像Url](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** 獲取物件檢測專案的預測。

## <a name="use-the-new-iteration-publishing-workflow"></a>使用新的反覆運算發佈工作流

2.x API 使用預設反覆運算或指定的反覆運算 ID 選擇用於預測的反覆運算。 從 3.0 開始，我們採用了發佈流，通過該流，您首先從培訓 API 中以指定名稱發佈反覆運算。 然後，將名稱傳遞給預測方法，以指定要使用的反覆運算。

> [!IMPORTANT]
> 3.0 API 不使用預設反覆運算功能。 在我們棄用較舊的 API 之前，可以繼續使用 2.x API 將反覆運算作為預設值切換。 這些 API 將維護一段時間，您可以調用**[UpdateTimea](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 方法將反覆運算標記為預設值。

### <a name="publish-an-iteration"></a>發佈反覆運算

訓練反覆運算後，可以使用**[Publish 反覆運算](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 方法將其可用於預測。 要發佈反覆運算，您需要預測資源識別碼，該 ID 可在自訂 Vision 網站的設置頁面上提供。

![自訂視覺網站設置頁面，概述預測資源識別碼。](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> 還可以通過訪問自訂視覺預測資源和選擇**屬性**從[Azure 門戶](https://portal.azure.com)獲取此資訊。

發佈反覆運算後，應用可以通過在其預測 API 呼叫中指定名稱來使用它進行預測。 要使反覆運算對預測調用不可用，請使用**["未發佈反覆運算](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**"API。

## <a name="next-steps"></a>後續步驟

* [培訓 API 參考文檔 （REST）](https://go.microsoft.com/fwlink/?linkid=865446)
* [預測 API 參考文檔 （REST）](https://go.microsoft.com/fwlink/?linkid=865445)