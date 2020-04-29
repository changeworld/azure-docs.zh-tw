---
title: 評估自訂語音語音服務的精確度
titleSuffix: Azure Cognitive Services
description: 在本檔中，您將瞭解如何數量測量語音轉換文字模型或自訂模型的品質。 必須提供音訊 + 人為標記的轉譯資料，才能測試精確度，以及30分鐘到5小時的代表性音訊。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "74806091"
---
# <a name="evaluate-custom-speech-accuracy"></a>評估自訂語音精確度

在本檔中，您將瞭解如何數量測量 Microsoft 的語音轉換文字模型或自訂模型的品質。 必須提供音訊 + 人為標記的轉譯資料，才能測試精確度，以及30分鐘到5小時的代表性音訊。

## <a name="what-is-word-error-rate-wer"></a>什麼是文字錯誤率（WER）？

用來測量模型精確度的業界標準是「*單字錯誤率*」（WER）。 WER 會計算辨識期間所識別的不正確單字數目，然後除以人為標記的文字記錄中所提供的單字總數。 最後，該數位會乘以100% 來計算 WER。

![WER 公式](./media/custom-speech/custom-speech-wer-formula.png)

不正確識別的單字分為三個類別：

* 插入（I）：假設文字記錄中未正確新增的字詞
* 刪除（D）：假設文字記錄中未偵測到的字詞
* 替代（S）：在參考與假設之間取代的單字

範例如下：

![不正確識別的單字範例](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>解決錯誤並改善 WER

您可以使用機器辨識結果中的 WER，來評估您的應用程式、工具或產品所使用的模型品質。 5%-10% 的 WER 會被視為良好的品質，並可供使用。 可以接受20% 的 WER，但您可能會想要考慮其他訓練。 30% 或更多信號的 WER 不佳品質，而且需要自訂和訓練。

如何散發錯誤是很重要的。 遇到許多刪除錯誤時，通常是因為音訊信號強度不佳。 若要解決此問題，您必須收集更接近來源的音訊資料。 插入錯誤表示音訊已記錄在雜訊的環境中，而且可能出現 crosstalk，而導致辨識問題。 當特定領域詞彙的樣本數已提供為人為標記的轉譯或相關文字時，通常會發生替代錯誤。

藉由分析個別檔案，您可以判斷哪些類型的錯誤存在，以及哪些錯誤對特定檔案而言是唯一的。 瞭解檔案層級的問題將可協助您以改良功能為目標。

## <a name="create-a-test"></a>建立測試

如果您想要測試 Microsoft 語音轉換文字基準模型的品質，或您已定型的自訂模型，您可以並排比較兩個模型來評估精確度。 比較包括 WER 和辨識結果。 一般而言，自訂模型會與 Microsoft 的基準模型進行比較。

若要並排評估模型：

1. 登入[自訂語音入口網站](https://speech.microsoft.com/customspeech)。
2. 流覽至**語音轉換文字 > 自訂語音 > 測試**。
3. 按一下 [**加入測試**]。
4. 選取 [**評估精確度**]。 為測試指定名稱、描述，然後選取您的音訊 + 人為標記的轉譯資料集。
5. 選取最多兩個您想要測試的模型。
6. 按一下 [建立]  。

成功建立您的測試之後，您可以並排比較結果。

## <a name="side-by-side-comparison"></a>並存比較

測試完成之後，[狀態] 變更為 [*成功*] 時，您會發現測試中包含的兩個模型都有一個 WER 數位。 按一下 [測試名稱] 以查看 [測試詳細資料] 頁面。 此詳細資料頁面會列出資料集內的所有語句，指出兩個模型與所提交資料集之轉譯的辨識結果。 若要協助檢查並存比較，您可以切換各種錯誤類型，包括插入、刪除和替代。 藉由接聽音訊並比較每個資料行中的辨識結果，其中會顯示兩個語音轉換文字模型的人標記轉譯和結果，您可以決定哪一個模型符合您的需求，以及需要額外的定型和改進。

## <a name="next-steps"></a>後續步驟

* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

* [準備和測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
