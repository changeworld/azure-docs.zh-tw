---
title: 為自訂語音 - 語音服務訓練模型
titleSuffix: Azure Cognitive Services
description: 訓練語音到文本模型可以提高 Microsoft 基線模型或自訂模型的識別準確性。 模型使用人工標記的轉錄和相關文本進行訓練。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137760"
---
# <a name="train-a-model-for-custom-speech"></a>讓自訂語音模型定型

訓練語音到文本模型可以提高 Microsoft 基線模型的識別準確性。 模型使用人工標記的轉錄和相關文本進行訓練。 這些資料集以及以前上傳的音訊資料用於優化和訓練語音到文本模型以識別單詞、短語、首字母縮略詞、名稱和其他特定于產品的術語。 您提供的域內資料集越多（與使用者將說什麼和您希望識別的內容相關的資料），您的模型就越準確，從而改進了識別效果。 請記住，通過將不相關的資料輸入到您的訓練中，可以減少或損害模型的準確性。

## <a name="use-training-to-resolve-accuracy-issues"></a>使用培訓解決準確性問題

如果您遇到模型的識別問題，請使用人工標記的腳本和相關資料進行附加培訓，有助於提高準確性。 使用此表可以確定要使用哪個資料集來解決問題：

| 使用案例 | 資料類型 |
| -------- | --------- |
| 提高行業特定詞彙和語法（如醫學術語或 IT 術語）的識別準確性。 | 相關文本（句子/陳述） |
| 定義具有非標準發音的單詞或術語（如產品名稱或首字母縮略詞）的拼音和顯示形式。 | 相關文本（發音） |
| 提高說話風格、口音或特定背景雜音的識別準確性。 | 音訊 + 人工標記的腳本 |

> [!IMPORTANT]
> 如果您尚未上傳資料集，請參閱[準備和測試您的資料](how-to-custom-speech-test-data.md)。 本文檔提供有關上傳資料的說明，以及創建高品質資料集的指南。

## <a name="train-and-evaluate-a-model"></a>定型及評估模型

訓練模型的第一步是上載訓練資料。 使用[準備和測試資料](how-to-custom-speech-test-data.md)，以備行進說明，以準備人工標記的轉錄和相關文本（單詞和發音）。 上傳培訓資料後，請按照以下說明開始培訓模型：

1. 登錄到[自訂語音門戶](https://speech.microsoft.com/customspeech)。
2. 導航到**語音到文本>自訂語音>培訓**。
3. 按一下 **"火車"模型**。
4. 接下來，為您的培訓提供**名稱**和**說明**。
5. 在 **"方案和基線模型**"下拉式功能表中，選擇最適合域的方案。 如果您不確定要選擇哪種方案，請選擇 **"常規**"。 基線模型是培訓的起點。 如果您沒有首選項，則可以使用最新首選項。
6. 在 **"選擇訓練資料**"頁中，選擇一個或多個音訊和人工標記的轉錄資料集，以便用於培訓。
7. 培訓完成後，您可以選擇對新訓練的模型執行準確性測試。 此為選用步驟。
8. 選擇 **"創建"** 可生成自訂模型。

"訓練"表顯示一個新條目，該條目對應于此新創建的模型。 該表還顯示狀態：處理、成功、失敗。

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>評估經過培訓的模型的準確性

您可以使用以下文檔檢查資料並評估模型準確性：

- [檢查資料](how-to-custom-speech-inspect-data.md)
- [評估您的資料](how-to-custom-speech-evaluate-data.md)

如果選擇測試準確性，請務必選擇不同于與模型一起使用的聲學資料集，以便對模型的性能進行逼真的感知。

## <a name="next-steps"></a>後續步驟

- [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

- [準備和測試資料](how-to-custom-speech-test-data.md)
- [檢查資料](how-to-custom-speech-inspect-data.md)
- [評估您的資料](how-to-custom-speech-evaluate-data.md)
- [定型模型](how-to-custom-speech-train-model.md)
