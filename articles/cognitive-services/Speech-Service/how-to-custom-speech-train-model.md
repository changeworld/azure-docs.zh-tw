---
title: 為自訂語音語音服務定型模型
titleSuffix: Azure Cognitive Services
description: 訓練語音轉換文字模型可以改善 Microsoft 基準模型或自訂模型的辨識準確度。 模型會使用人類加上標籤的轉譯和相關文字來定型。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 09867d6739998902ea0fd11281d1b975866c1242
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722621"
---
# <a name="train-a-model-for-custom-speech"></a>定型自訂語音的模型

訓練語音轉換文字模型可以改善 Microsoft 基準模型的辨識準確度。 模型會使用人類加上標籤的轉譯和相關文字來定型。 這些資料集連同先前上傳的音訊資料，可用來精簡和定型語音轉換文字模型，以辨識單字、片語、縮略字、名稱和其他產品特定詞彙。 您所提供的網域中的資料集越多（與使用者所說的內容和您預期辨識的內容有關），您的模型就越精確，這會導致辨識改善。 請記住，藉由將不相關的資料放入您的訓練中，您可以降低或損害模型的精確度。

## <a name="use-training-to-resolve-accuracy-issues"></a>使用訓練來解決精確度問題

如果您在模型中遇到辨識問題，使用人為標記的文字記錄和相關資料來進行其他訓練，有助於改善正確性。 使用此資料表來判斷要用來解決問題的資料集：

| 使用案例 | 資料類型 |
| -------- | --------- |
| 改善業界特定詞彙和文法的辨識準確度，例如醫學術語或 IT 專業術語。 | 相關文字（句子/語句） |
| 定義具有非標準發音之單字或詞彙的拼音文字和顯示形式，例如產品名稱或縮略字。 | 相關文字（發音） |
| 改善說話風格、強調或特定背景雜音的辨識準確度。 | 音訊 + 人為標記的文字記錄 |

> [!IMPORTANT]
> 如果您尚未上傳資料集，請參閱[準備和測試您的資料](how-to-custom-speech-test-data.md)。 本檔提供上傳資料的指示，以及建立高品質資料集的指導方針。

## <a name="train-and-evaluate-a-model"></a>定型及評估模型

定型模型的第一個步驟是上傳定型資料。 請使用[準備和測試您的資料](how-to-custom-speech-test-data.md)，以取得準備人為標記的轉譯和相關文字（語句和發音）的逐步指示。 在您上傳定型資料之後，請遵循下列指示來開始訓練您的模型：

1. 登入[自訂語音入口網站](https://speech.microsoft.com/customspeech)。
2. 流覽至**語音轉換文字 > 自訂語音 > 訓練**。
3. 按一下 [**定型模型**]。
4. 接下來，為您的訓練提供**名稱**和**描述**。
5. 從 [**案例和基準模型**] 下拉式功能表中，選取最適合您的網域的案例。 如果您不確定要選擇哪一個案例，請選取 **[一般**]。 基準模型是訓練的起點。 最新的模型通常是最佳選擇。
6. 從 [**選取定型資料**] 頁面中，選擇一或多個音訊 + 個人化的轉譯資料集，您想要用來進行定型。
7. 定型完成後，您可以選擇對新定型的模型執行精確度測試。 這是選擇性步驟。
8. 選取 [**建立**] 以建立您的自訂模型。

定型資料表會顯示新的專案，並對應到這個新建立的模型。 資料表也會顯示狀態： [處理中]、[成功]、[失敗]。

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>評估定型模型的精確度

您可以使用下列檔來檢查資料並評估模型的精確度：

- [檢查您的資料](how-to-custom-speech-inspect-data.md)
- [評估您的資料](how-to-custom-speech-evaluate-data.md)

如果您選擇測試精確度，請務必選取與您的模型所用不同的聲場資料集，以實際瞭解模型的效能。

## <a name="next-steps"></a>後續步驟

- [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

- [準備和測試您的資料](how-to-custom-speech-test-data.md)
- [檢查您的資料](how-to-custom-speech-inspect-data.md)
- [評估您的資料](how-to-custom-speech-evaluate-data.md)
- [定型模型](how-to-custom-speech-train-model.md)
