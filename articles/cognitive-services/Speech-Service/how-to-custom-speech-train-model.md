---
title: 將自訂語音語音服務的模型定型
titleSuffix: Azure Cognitive Services
description: 訓練語音轉換文字模型可以改善 Microsoft 基準模型或自訂模型的辨識精確度。 使用人為標記的轉譯和相關文字來定型模型。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: bf9209e0c256412ccb06ea62a197046a7b012e00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84629030"
---
# <a name="train-a-model-for-custom-speech"></a>定型自訂語音的模型

訓練語音轉換文字模型可以改善 Microsoft 基準模型的辨識精確度。 使用人為標記的轉譯和相關文字來定型模型。 這些資料集和先前上傳的音訊資料，可用來精簡和定型語音轉換文字模型。

## <a name="use-training-to-resolve-accuracy-issues"></a>使用定型來解決精確度問題

如果您遇到模型的辨識問題，使用人標示的文字記錄和相關資料進行其他定型，有助於改善精確度。 您可以使用此資料表來判斷要使用哪一個資料集來解決 (s) 的問題：

| 使用案例 | 資料類型 |
| -------- | --------- |
| 改善業界專屬詞彙和文法的辨識準確度，例如醫療術語或 IT 專業術語。 | 相關文字 (句子/語句)  |
| 定義包含非標準發音（例如產品名稱或縮略字）的單字或詞彙的語音和顯示形式。 | 相關文字 (發音)  |
| 改善說話樣式、重音或特定背景雜訊的辨識精確度。 | 音訊 + 人類標記的文字記錄 |

> [!IMPORTANT]
> 如果您尚未上傳資料集，請參閱 [準備和測試您的資料](how-to-custom-speech-test-data.md)。 本檔提供上傳資料的指示，以及建立高品質資料集的指導方針。

## <a name="train-and-evaluate-a-model"></a>定型及評估模型

定型模型的第一個步驟是上傳定型資料。 使用「 [準備和測試您的資料](how-to-custom-speech-test-data.md) 」以取得逐步指示，以準備人標記的轉譯和相關文字 (語句和發音) 。 上傳定型資料之後，請遵循下列指示來開始訓練您的模型：

1. 登入 [自訂語音入口網站](https://speech.microsoft.com/customspeech)。
2. 流覽至 **語音轉換文字 > 自訂語音 > [專案名稱] > 訓練**。
3. 按一下 [ **定型模型**]。
4. 接下來，為您的訓練提供 **名稱** 和 **描述**。
5. 從 [ **案例和基準模型** ] 下拉式功能表中，選取最適合您的網域的案例。 如果您不確定要選擇哪一種案例，請選取 **[一般**]。 基準模型是定型的起點。 最新的模型通常是最佳選擇。
6. 從 [ **選取定型資料** ] 頁面中，選擇您想要用於定型的一或多個音訊 + 人標示轉譯資料集。
7. 定型完成後，您可以選擇在新定型的模型上執行精確度測試。 此為選用步驟。
8. 選取 [ **建立** ] 以建立您的自訂模型。

定型資料表會顯示對應至這個新建立模型的新專案。 此資料表也會顯示狀態：「處理中」、「成功」、「失敗」。

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>評估定型模型的精確度

您可以使用下列檔來檢查資料並評估模型精確度：

- [檢查您的資料](how-to-custom-speech-inspect-data.md)
- [評估您的資料](how-to-custom-speech-evaluate-data.md)

如果您選擇測試精確度，請務必選取與您在模型中使用的聲場資料集不同，以取得模型效能的實際意義。

## <a name="next-steps"></a>接下來的步驟

- [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

- [準備及測試您的資料](how-to-custom-speech-test-data.md)
- [檢查您的資料](how-to-custom-speech-inspect-data.md)
- [評估您的資料](how-to-custom-speech-evaluate-data.md)
- [定型模型](how-to-custom-speech-train-model.md)
