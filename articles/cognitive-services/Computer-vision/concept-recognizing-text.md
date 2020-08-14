---
title: " (OCR) 的光學字元辨識-電腦視覺"
titleSuffix: Azure Cognitive Services
description: 與光學字元辨識相關的概念 (使用電腦視覺 API 的列印和手寫文字之影像和檔的 OCR) 。
services: cognitive-services
author: PatrickFarley
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9f9ebff77f54d86c3c4ed45fb5190de1900934e9
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207222"
---
# <a name="optical-character-recognition-ocr"></a>光學字元辨識 (OCR)

Azure 的電腦視覺 API 包含光學字元辨識 (OCR) 功能，可從影像中將列印或手寫文字解壓縮。 您可以從影像中解壓縮文字，例如授權盤子的相片或具有序號的容器，以及檔-發票、帳單、財務報告、文章等等。 

## <a name="read-api"></a>讀取 API 

電腦視覺 [讀取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 是 Azure 的最新 OCR 技術，會以數種語言來解壓縮列印的文字 () 、手寫文字僅 (英文) 、數位和貨幣符號，從影像和多頁 PDF 檔。 它已優化，可使用混合語言從文字密集影像和多頁 PDF 檔解壓縮文字。 它支援在相同的影像或檔中偵測列印和手寫文字。

![OCR 如何使用已解壓縮的文字將影像和檔轉換成結構化輸出](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>輸入需求
讀取 API 的 **讀取** 作業會將影像和檔作為其輸入。 它們具有下列需求：

* 支援的檔案格式： JPEG、PNG、BMP、PDF 和 TIFF
* 若是 PDF 和 TIFF，則會處理最多2000個頁面。 對於免費層訂閱者，只會處理前兩個頁面。
* 檔案大小必須小於 50 MB，以及至少 50 x 50 圖元和最多 10000 x 10000 圖元的維度。
* PDF 尺寸最多必須是 17 x 17 英寸，對應于合法或 A3 紙張大小和較小。

> [!NOTE]
> **語言輸入** 
>
> [讀取](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)作業具有語言的選擇性要求參數。 這是檔中文字的 BCP-47 語言代碼。 「讀取」支援自動語言識別和多語系檔，因此，只有在您想要強制檔以該特定語言來處理時，才提供語言代碼。

## <a name="the-read-operation"></a>讀取作業

[讀取](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)作業會以影像或 PDF 檔做為輸入，並以非同步方式解壓縮文字。 呼叫會傳回，並具有稱為的回應標頭欄位 `Operation-Location` 。 此 `Operation-Location` 值是一個 URL，其中包含要在下一個步驟中使用的作業識別碼。

|回應標頭| 結果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.0/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-read-results-operation"></a>取得讀取結果作業

第二個步驟是呼叫「 [取得讀取結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) 」作業。 這項作業會將讀取作業所建立的作業識別碼當做輸入。 它會傳回 JSON 回應，其中包含具有下列可能值的 **狀態** 欄位。 您會反復呼叫此作業，直到它傳回具有 **成功** 值的為止。 請使用1到2秒的間隔，以避免超過每秒要求數 (RPS) 速率。

|欄位| 類型 | 可能值 |
|:-----|:----:|:----|
|status | 字串 | notStarted：操作尚未啟動。 |
| |  | 正在執行：正在處理作業。 |
| |  | failed：作業失敗。 |
| |  | 成功：作業已成功。 |

> [!NOTE]
> 免費層會將要求速率限制為每分鐘20個呼叫。 付費層允許每秒10個要求 (RPS) 可以在要求時增加。 使用 Azure 支援通道或您的帳戶小組，每秒要求較高的要求 (RPS) 速率。

當 [ **狀態** ] 欄位具有 [ **成功** ] 值時，JSON 回應會包含您的影像或檔中已解壓縮的文字內容。 JSON 回應會維護已辨識單字的原始行分組。 其中包含已解壓縮的文字行及其周框方塊座標。 每一行都包含所有已解壓縮的文字及其座標和信賴分數。

### <a name="sample-json-output"></a>範例 JSON 輸出

請參閱下列成功 JSON 回應的範例：

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

請遵循 [解壓縮印刷和手寫文字](./QuickStarts/CSharp-hand-text.md) 快速入門，使用 c # 和 REST API 來執行 OCR。

## <a name="language-support"></a>語言支援

### <a name="printed-text"></a>列印的文字
[讀取 3.0 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)支援以英文、西班牙文、德文、法文、義大利文、葡萄牙文和荷蘭文語言來解壓縮印刷文字。 

[Read 3.1 API 公開預覽](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)加入了簡體中文的支援。 如果您的案例需要支援更多語言，請參閱 [OCR API](#ocr-api) 一節。 

如需 OCR 支援語言的完整清單，請參閱 [支援的語言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) 。

### <a name="handwritten-text"></a>手寫文字
讀取作業目前支援以英文專用的方式來解壓縮手寫文字。

## <a name="integration-options"></a>整合選項

### <a name="use-the-rest-api-or-client-sdk"></a>使用 REST API 或用戶端 SDK
對於大部分的客戶而言， [Read 3.x REST API](./QuickStarts/CSharp-hand-text.md) 是慣用的選項，因為這可讓您輕鬆地進行整合和快速的生產力。 當您專注于滿足客戶的需求時，Azure 和電腦視覺服務會處理規模、效能、資料安全性和合規性需求。

### <a name="use-containers-for-on-premise-deployment"></a>使用容器進行內部部署
[讀取 2.0 Docker 容器 (預覽) ](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)可讓您在自己的本機環境中部署新的 OCR 功能。 容器非常適合用於特定的安全性和資料控管需求。

## <a name="read-ocr-examples"></a>閱讀 OCR 範例

### <a name="text-from-images"></a>影像中的文字

下列讀取 API 輸出顯示影像中具有不同文字角度、色彩和字型的已解壓縮文字。

![以不同色彩和角度顯示的數個單字影像，並列出已解壓縮的文字](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>檔中的文字

讀取 API 也可以接受 PDF 檔作為輸入。

![已列出已解壓縮文字的發票檔](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>手寫文字

讀取作業會從影像中解壓縮手寫文字， (目前僅以英文) 。

![手寫便箋的影像，並列出已解壓縮的文字](./Images/handwritten-example.png)

### <a name="printed-text"></a>列印的文字

讀取作業可以用數種不同的語言來解壓縮列印的文字。

![西班牙文文字方塊的影像，並列出已解壓縮的文字](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>混合語言檔

讀取 API 支援包含多種不同語言的影像和檔，通常稱為混合語言檔。 其運作方式是將檔中的每個文字行分類成偵測到的語言，再將其文字內容解壓縮。

![數種語言的片語影像，其中列出已解壓縮的文字](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)會使用較舊的辨識模型、僅支援影像，並以同步方式執行，並立即傳回偵測到的文字。 請參閱 [OCR 支援的語言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) ，然後閱讀 API。

## <a name="data-privacy-and-security"></a>資料隱私權和安全性

就像所有的認知服務一樣，使用讀取/OCR 服務的開發人員應該要瞭解 Microsoft 對於客戶資料的原則。 若要深入瞭解，請參閱 [Microsoft 信任中心](https://www.microsoft.com/trust-center/product-overview) 上的認知服務頁面。

> [!NOTE]
> 電腦 Vison 2.0 RecognizeText 作業正在淘汰，而改用本文所涵蓋的新讀取 API。 現有客戶應 [轉換為使用讀取作業](upgrade-api-versions.md)。

## <a name="next-steps"></a>後續步驟

- 瞭解 [讀取 3.0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)。
- 瞭解 [Read 3.1 公開預覽 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) ，並新增對簡體中文的支援。
- 遵循 [解壓縮文字](./QuickStarts/CSharp-hand-text.md) 快速入門，使用 c #、JAVA、JavaScript 或 Python 搭配 REST API 來執行 OCR。
