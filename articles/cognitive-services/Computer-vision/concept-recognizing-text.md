---
title: 光學字元辨識 (OCR) -電腦視覺
titleSuffix: Azure Cognitive Services
description: 與光學字元辨識相關的概念 (使用電腦視覺 API 列印和手寫文字的影像和檔的 OCR) 。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 37a989082b63dc101bb519fea1cc4ef16c76ae49
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621530"
---
# <a name="optical-character-recognition-ocr"></a>光學字元辨識 (OCR)

Azure 的電腦視覺 API 包含光學字元辨識 (OCR) 功能，可從影像中解壓縮印刷或手寫文字。 您可以將影像中的文字（例如授權板的相片或具有序號的容器），以及檔-發票、帳單、財務報表、文章等專案解壓縮。

## <a name="read-api"></a>讀取 API 

電腦視覺 [READ API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) 是 Azure 最新的 OCR 技術 ([瞭解有哪些新](./whats-new.md) 的) 可將列印的文字解壓縮 (數種語言) 、手寫文字 (僅英文) 、數位和貨幣符號，以及影像和多頁 PDF 檔。 它已優化，可將文字從大量文字的影像和具有混合語言的多頁 PDF 檔解壓縮。 它支援在相同的影像或檔中偵測列印和手寫的文字。

![OCR 如何使用已解壓縮的文字將影像和檔轉換成結構化的輸出](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>輸入需求
**讀取** 呼叫會將影像和檔做為輸入。 它們具有下列需求：

* 支援的檔案格式： JPEG、PNG、BMP、PDF 和 TIFF
* 針對 PDF 和 TIFF 檔案，最多2000頁 (只會處理免費層) 的前兩個頁面。
* 檔案大小必須小於 50 MB (4 MB 適用于免費層) 和維度至少 50 x 50 圖元，以及最多 10000 x 10000 圖元。 
* PDF 尺寸必須最多為 17 x 17 英寸，對應于合法或 A3 紙張大小，且較小。

### <a name="read-32-preview-allows-selecting-pages"></a>讀取3.2 預覽可讓您選取頁面 (s) 
使用 [讀取3.2 預覽 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005)時，對於大型的多頁檔，您可以提供特定頁碼或頁面範圍做為輸入參數，只將這些頁面中的文字解壓縮。 除了選用的語言參數之外，這還是新的輸入參數。

> [!NOTE]
> **語言輸入** 
>
> [讀取呼叫](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)有語言的選擇性要求參數。 這是檔中文字的 BCP-47 語言代碼。 Read 支援自動語言識別和多語系檔，因此，如果您想要強制檔以該特定語言來處理，請只提供語言代碼。

## <a name="the-read-call"></a>讀取呼叫

讀取 API 的 [讀取呼叫](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) 會採用影像或 PDF 檔做為輸入，並以非同步方式解壓縮文字。 呼叫會傳迴響應標頭欄位，稱為 `Operation-Location` 。 此 `Operation-Location` 值為 URL，其中包含要在下一個步驟中使用的作業識別碼。

|回應標頭| 結果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> [電腦視覺定價](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)頁面包含可供讀取的定價層。 每個分析的影像或頁面都是一筆交易。 如果您使用包含100頁面的 PDF 或 TIFF 檔來呼叫作業，讀取作業會將其計算為100筆交易，而您將會支付100筆交易的費用。 如果您對作業進行了50次呼叫，而每個呼叫提交了具有100頁面的檔，則會向您收取 50 X 100 = 5000 交易的費用。

## <a name="the-get-read-results-call"></a>取得讀取結果呼叫

第二個步驟是呼叫「 [取得讀取結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) 」作業。 這項作業會以讀取作業所建立的作業識別碼作為輸入。 它會傳回 JSON 回應，其中包含具有下列可能值的 **狀態** 欄位。 您會反復呼叫此操作，直到它傳回 **成功** 的值為止。 使用1至2秒的間隔，以避免每秒超過要求 (RPS) 速率。

|欄位| 類型 | 可能值 |
|:-----|:----:|:----|
|status | 字串 | notStarted：作業尚未啟動。 |
| |  | 正在執行：正在處理作業。 |
| |  | failed：作業已失敗。 |
| |  | 成功：作業已成功。 |

> [!NOTE]
> 免費層會將要求速率限制為每分鐘20次呼叫。 付費層允許每秒10個要求 (RPS) ，可在要求時增加。 使用 Azure 支援通道或您的帳戶小組，以每秒要求更高的要求， (RPS) 的速率。

當 [ **狀態** ] 欄位的值為 [ **成功** ] 時，JSON 回應會包含來自您的影像或檔的已解壓縮文字內容。 JSON 回應會維護已辨識字組的原始行群組。 它包含已解壓縮的文字線條和其周框方塊座標。 每一行文字都包含所有已解壓縮的文字，以及其座標和信賴分數。

> [!NOTE]
> 提交給作業的資料 `Read` 會暫時加密並儲存在 rest 中，並在48小時內刪除。 這可讓您的應用程式以服務回應的一部分取出已解壓縮的文字。

## <a name="sample-json-output"></a>範例 JSON 輸出

請參閱下列成功 JSON 回應的範例：

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
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
### <a name="read-32-preview-adds-text-line-style-latin-languages-only"></a>閱讀 3.2 preview 只會將文字行樣式新增 (拉丁語言) 
[Read 3.2 PREVIEW API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005)會輸出 **外觀** 物件，以分類每個文字行是列印或手寫樣式，以及信賴分數。 這項功能僅支援拉丁語言。

開始使用 [電腦視覺 REST API 或用戶端程式庫快速入門](./quickstarts-sdk/client-library.md) ，開始將 OCR 功能整合到您的應用程式中。

## <a name="supported-languages-for-print-text"></a>列印文字支援的語言
[讀取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)支援以英文、西班牙文、德文、法文、義大利文、葡萄牙文和荷蘭文語言來解壓縮印刷文字。

如需 OCR 支援語言的完整清單，請參閱 [支援的語言](./language-support.md#optical-character-recognition-ocr) 。

### <a name="read-32-preview-adds-simplified-chinese-and-japanese"></a>閱讀 3.2 preview 新增簡體中文和日文
[Read 3.2 API 公開預覽版](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005)新增了簡體中文和日文的支援。 如果您的案例需要支援更多語言，請參閱 [OCR API](#ocr-api) 一節。 

## <a name="supported-languages-for-handwritten-text"></a>手寫文字支援的語言
讀取作業目前支援專門以英文解壓縮手寫文字。

## <a name="use-the-rest-api-and-sdk"></a>使用 REST API 和 SDK
[Read 3.x REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)是最適合大多數客戶的選項，因為這種方法可讓您輕鬆整合並快速提高產能。 當您專注于滿足客戶的需求時，Azure 和電腦視覺服務可處理規模調整、效能、資料安全性和合規性需求。

## <a name="deploy-on-premise-with-docker-containers"></a>使用 Docker 容器部署內部部署
[讀取 Docker 容器 (預覽版) ](./computer-vision-how-to-install-containers.md)可讓您在自己的本機環境中部署新的 OCR 功能。 容器非常適合用於特定的安全性和資料控管需求。

## <a name="example-outputs"></a>範例輸出

### <a name="text-from-images"></a>影像中的文字

下列讀取 API 輸出會顯示具有不同文字角度、色彩和字型的影像所解壓縮的文字。

![多個單字的影像（不同的色彩和角度），並列出已解壓縮的文字](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>檔中的文字

Read API 也可以將 PDF 檔做為輸入。

![已列出已解壓縮文字的發票檔](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>手寫文字

讀取作業會從影像中解壓縮手寫文字， (目前只有英文) 。

![手寫便箋的影像，並列出已解壓縮的文字](./Images/handwritten-example.png)

### <a name="printed-text"></a>列印的文字

讀取作業可以用數種不同的語言來解壓縮列印的文字。

![西班牙文文字方塊的影像，並列出已解壓縮的文字](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>混合語言檔

讀取 API 支援包含多種不同語言的影像和檔，通常稱為混合語言檔。 其運作方式是將檔中的每個文字行分類為偵測到的語言，再將其文字內容解壓縮。

![數種語言的片語影像，並列出已解壓縮的文字](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR API

[OCR API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d)會使用較舊的辨識模型、僅支援影像，並以同步方式執行，並立即以偵測到的文字傳回。 請參閱 [OCR 支援的語言](./language-support.md#optical-character-recognition-ocr) ，然後讀取 API。

## <a name="data-privacy-and-security"></a>資料隱私權和安全性

和所有認知服務一樣，使用「讀取/OCR」服務的開發人員應該要瞭解 Microsoft 對於客戶資料的政策。 若要深入瞭解，請參閱 [Microsoft 信任中心](https://www.microsoft.com/trust-center/product-overview) 的認知服務頁面。

> [!NOTE]
> 電腦 Vison 2.0 RecognizeText 作業正在淘汰，以促進本文涵蓋的新讀取 API。 現有的客戶應該 [轉換成使用讀取作業](upgrade-api-versions.md)。

## <a name="next-steps"></a>後續步驟

- [電腦視覺 REST API 或用戶端程式庫快速入門](./quickstarts-sdk/client-library.md)開始使用。
- 瞭解 [讀取 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)。
- 深入瞭解 [Read 3.2 公開預覽版 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) ，並新增對簡體中文和日文的支援。