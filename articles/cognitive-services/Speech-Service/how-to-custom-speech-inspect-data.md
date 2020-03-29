---
title: 檢查自訂語音 - 語音服務的資料品質
titleSuffix: Azure Cognitive Services
description: 自訂語音提供了工具，允許您通過將音訊資料與相應的識別結果進行比較來直觀地檢查模型的識別品質。 您可以播放上傳的音訊，並確定提供的識別結果是否正確。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806074"
---
# <a name="inspect-custom-speech-data"></a>檢查自訂語音資料

> [!NOTE]
> 此頁面假定您已閱讀["自訂語音準備測試資料"，](how-to-custom-speech-test-data.md)並上載資料集進行檢查。

自訂語音提供了工具，允許您通過將音訊資料與相應的識別結果進行比較來直觀地檢查模型的識別品質。 在[自訂語音門戶](https://speech.microsoft.com/customspeech)中，您可以播放上傳的音訊並確定提供的識別結果是否正確。 此工具允許您快速檢查 Microsoft 的基準語音到文本模型或經過訓練的自訂模型的品質，而無需轉錄任何音訊資料。

在本文檔中，您將學習如何使用以前上載的培訓資料直觀地檢查模型的品質。

在此頁上，您將學習如何直觀地檢查 Microsoft 的基準語音到文本模型和/或您培訓的自訂模型的品質。 您將使用上載到 **"資料"** 選項卡的資料進行測試。

## <a name="create-a-test"></a>創建測試

按照以下說明創建測試：

1. 登錄到[自訂語音門戶](https://speech.microsoft.com/customspeech)。
2. 導航到**語音到文本>自訂語音>測試**。
3. 按一下 **"添加測試**"。
4. 選擇 **"檢查品質（僅音訊資料）**"。 為測試指定名稱、說明並選擇音訊資料集。
5. 最多選擇兩個要測試的型號。
6. 按一下 **[建立]**。

成功創建測試後，可以並排比較模型。

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>並排模型比較

當測試狀態_成功時_，按一下測試項名稱以查看測試的詳細資訊。 此詳細資訊頁列出了資料集中的所有陳述，指示兩個模型的識別結果以及提交的資料集的轉錄。

為了説明檢查並排比較，可以切換各種錯誤類型，包括插入、刪除和替換。 通過收聽音訊並比較每列中的識別結果（顯示人工標記的轉錄和兩個語音到文本模型的結果），您可以決定哪個模型滿足您的需求以及需要改進的地方。

檢查品質測試對於驗證語音辨識終結點的品質是否足以適用于應用程式非常有用。 要客觀測量準確性（需要轉錄音訊），請按照[評估精度](how-to-custom-speech-evaluate-data.md)中的說明進行操作。

## <a name="next-steps"></a>後續步驟

- [評估您的資料](how-to-custom-speech-evaluate-data.md)
- [定型模型](how-to-custom-speech-train-model.md)
- [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

- [為自訂語音準備測試資料](how-to-custom-speech-test-data.md)
