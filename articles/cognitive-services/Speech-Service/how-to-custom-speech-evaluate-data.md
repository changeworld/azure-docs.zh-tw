---
title: 評估自訂語音-語音服務的精確度
titleSuffix: Azure Cognitive Services
description: 在本檔中，您將瞭解如何從數量測量語音轉換文字模型或自訂模型的品質。 必須提供音訊 + 人工標記的轉譯資料，才能測試精確度，並應提供30分鐘到5小時的代表性音訊。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: cadbe79bbe0af2b5cebacb3d0c7c4e910fc7dbb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85856840"
---
# <a name="evaluate-custom-speech-accuracy"></a>評估自訂語音精確度

在本檔中，您將瞭解如何從數量測量 Microsoft 語音轉換文字模型或自訂模型的品質。 必須提供音訊 + 人工標記的轉譯資料，才能測試精確度，並應提供30分鐘到5小時的代表性音訊。

## <a name="what-is-word-error-rate-wer"></a>什麼是 (WER) 的文字錯誤率？

測量模型精確度的產業標準是 (WER) 的 *文字* 錯誤率。 WER 會計算辨識期間所識別的錯誤單字數目，然後除以人為標記的文字記錄中提供的單字總數， (如下所示為 N) 。 最後，該數位會乘以100% 來計算 WER。

![WER 公式](./media/custom-speech/custom-speech-wer-formula.png)

錯誤識別的單字分為三種類別：

* 插入 (I) ：在假設文字記錄中未正確新增的單字
* 刪除 (D) ：在假設文字記錄中未偵測到的字詞
* 替代 (S) ：在參考和假設之間替代的單字

以下為範例：

![錯誤識別的單字範例](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>解決錯誤並改善 WER

您可以使用機器辨識結果中的 WER，來評估您的應用程式、工具或產品所使用的模型品質。 5%-10% 的 WER 被視為良好的品質，並已可供使用。 有20% 的 WER 是可接受的，不過您可能會想要考慮其他訓練。 30% 的 WER 或更多信號品質不佳，且需要自訂和定型。

錯誤的散發方式很重要。 遇到許多刪除錯誤時，通常是因為弱式音訊信號強度。 若要解決此問題，您必須收集接近來源的音訊資料。 插入錯誤表示音訊已記錄在雜訊的環境中，且 crosstalk 可能存在，而導致辨識問題。 當定義域特定詞彙的範例已提供為人為標記的轉譯或相關文字時，通常會發生替代錯誤。

藉由分析個別檔案，您可以判斷有何種類型的錯誤，以及特定檔案的哪些錯誤是唯一的。 瞭解檔案層級的問題可協助您以增強目標為目標。

## <a name="create-a-test"></a>建立測試

如果您想要測試 Microsoft 語音轉換文字基準模型的品質，或您已定型的自訂模型，您可以將兩個模型並排地進行比較，以評估精確度。 比較包括 WER 和辨識結果。 一般而言，自訂模型會與 Microsoft 的基準模型進行比較。

若要並行評估模型：

1. 登入 [自訂語音入口網站](https://speech.microsoft.com/customspeech)。
2. 流覽至 **語音轉換文字 > 自訂語音 > [專案名稱] > 測試**。
3. 按一下 [ **加入測試**]。
4. 選取 [ **評估精確度**]。 提供測試的名稱、描述，然後選取您的音訊 + 人類標示的轉譯資料集。
5. 選取最多兩個您想要測試的模型。
6. 按一下 [建立]。

成功建立您的測試之後，您可以並排比較結果。

## <a name="side-by-side-comparison"></a>並列比較

測試完成後，狀態變更為 [ *成功*] 時，您會發現測試中包含這兩個模型的 WER 數目。 按一下測試名稱以查看 [測試詳細資料] 頁面。 此詳細資料頁面會列出您資料集中的所有語句，表示兩個模型的辨識結果與所提交資料集的轉譯。 若要協助檢查並存比較，您可以切換各種錯誤類型，包括插入、刪除和替代。 藉由接聽音訊並比較每個資料行中的辨識結果，其中會顯示人為標記的轉譯，以及兩個語音轉換文字模型的結果，您可以決定哪一個模型符合您的需求，以及需要額外的訓練和改進。

## <a name="next-steps"></a>接下來的步驟

* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

* [準備及測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
