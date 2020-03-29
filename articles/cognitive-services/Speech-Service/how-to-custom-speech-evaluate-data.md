---
title: 評估自訂語音 - 語音服務的準確性
titleSuffix: Azure Cognitive Services
description: 在本文檔中，您將學習如何定量測量我們的語音到文本模型或自訂模型的品質。 需要音訊和人工標記的轉錄資料來測試準確性，並且應提供 30 分鐘到 5 小時的代表性音訊。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806091"
---
# <a name="evaluate-custom-speech-accuracy"></a>評估自訂語音準確性

在本文中，您將學習如何定量衡量 Microsoft 語音到文本模型或自訂模型的品質。 需要音訊和人工標記的轉錄資料來測試準確性，並且應提供 30 分鐘到 5 小時的代表性音訊。

## <a name="what-is-word-error-rate-wer"></a>什麼是字錯誤率 （WER）？

測量模型精度的行業標準是*Word 錯誤率*（WER）。 WER 計算識別過程中標識的錯誤單詞數，然後除以人標記腳本中提供的單詞總數。 最後，該數位乘以 100% 來計算 WER。

![WER 公式](./media/custom-speech/custom-speech-wer-formula.png)

識別錯誤的單詞分為三類：

* 插入（I）：在假設記錄中錯誤地添加的單詞
* 刪除 （D）：假設記錄中未檢測到的單詞
* 替換 （S）：引用和假設之間替代的單詞

以下是範例：

![錯誤識別的單詞示例](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>解決錯誤並改進 WER

您可以使用機器識別結果中的 WER 來評估與應用、工具或產品一起使用的模型的品質。 5%-10%的WER被認為是良好的品質，隨時可以使用。 20% 的 WER 是可以接受的，但您可能需要考慮其他培訓。 30% 或以上的 WER 表示品質不佳，需要自訂和培訓。

錯誤如何分佈非常重要。 當遇到許多刪除錯誤時，通常是由於音訊信號強度較弱。 要解決此問題，您需要收集離源更近的音訊資料。 插入錯誤意味著音訊是在嘈雜的環境中錄製的，並且可能存在串擾，從而導致識別問題。 當作為人工標記的轉錄或相關文本提供特定于域的術語樣本不足時，通常會遇到替換錯誤。

通過分析單個檔，可以確定存在哪些類型的錯誤，以及特定檔獨有的錯誤類型。 瞭解檔級別的問題將説明您確定改進目標。

## <a name="create-a-test"></a>創建測試

如果要測試 Microsoft 語音到文本基線模型或已訓練的自訂模型的品質，可以並排比較兩個模型以評估準確性。 比較包括 WER 和識別結果。 通常，自訂模型與 Microsoft 的基準模型進行比較。

要並排評估模型：

1. 登錄到[自訂語音門戶](https://speech.microsoft.com/customspeech)。
2. 導航到**語音到文本>自訂語音>測試**。
3. 按一下 **"添加測試**"。
4. 選擇 **"評估準確性**"。 為測試指定名稱、說明，並選擇音訊和人工標記的轉錄資料集。
5. 最多選擇兩個要測試的型號。
6. 按一下 **[建立]**。

成功創建測試後，可以並排比較結果。

## <a name="side-by-side-comparison"></a>並排比較

測試完成後，通過狀態更改為 *"成功"* 指示，您將找到測試中包含的兩個型號的 WER 編號。 按一下測試名稱以查看測試詳細資訊頁面。 此詳細資訊頁列出了資料集中的所有陳述，指示兩個模型的識別結果以及提交的資料集的轉錄。 為了説明檢查並排比較，可以切換各種錯誤類型，包括插入、刪除和替換。 通過收聽音訊並比較每一列中的識別結果（顯示人工標記的轉錄和兩個語音到文本模型的結果），您可以決定哪種模型滿足您的需求，以及哪些模型能夠進行額外的培訓和改進必填。

## <a name="next-steps"></a>後續步驟

* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

* [準備和測試資料](how-to-custom-speech-test-data.md)
* [檢查資料](how-to-custom-speech-inspect-data.md)
