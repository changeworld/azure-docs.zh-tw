---
title: 版面配置-表單辨識器
titleSuffix: Azure Cognitive Services
description: 瞭解使用表單辨識器 API 的版面配置分析相關概念-使用方式與限制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 60ef490bbba4dca4f01b82967ad6ea8636e7bd4e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353692"
---
# <a name="form-recognizer-layout-service"></a>表單辨識器版面佈建服務

Azure 表單辨識器可以使用其版面佈建服務，從檔中解壓縮文字、資料表、選取專案標記和結構資訊。 版面配置 API 可讓客戶採用各種格式的檔，並傳回結構化資料和檔的標記法。 它結合了強大的光學字元辨識與檔理解深度學習模型， [ (OCR) ](../computer-vision/concept-recognizing-text.md) 功能，以將文字、資料表、選取專案標記和檔結構解壓縮。 

## <a name="what-does-the-layout-service-do"></a>版面佈建服務的用途為何？

版面配置 API 會從具有異常精確度的檔中，將文字、資料表、選取標記和結構資訊解壓縮，並以組織的結構化 JSON 回應傳回它們。 檔可來自各種格式和品質，包括行動電話的影像、掃描的檔和數位 Pdf。 版面配置 API 將會從這些檔中解壓縮出結構化的輸出。

![版面配置範例](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>試做

若要試用表單辨識器版面佈建服務，請移至線上範例 UI 工具：

> [!div class="nextstepaction"]
> [範例 UI](https://fott-preview.azurewebsites.net/)

您將需要 Azure 訂用帳戶 ([建立免費) 的](https://azure.microsoft.com/free/cognitive-services)[表單](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)辨識器資源端點和金鑰，以試用表單辨識器版面配置 API。 

![範例 UI 螢幕擷取畫面;分析檔的文字、資料表和選取標記](./media/analyze-layout.png)

### <a name="input-requirements"></a>輸入需求 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>分析版面配置作業

「 [分析版面](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) 配置」作業會將檔 (影像、TIFF 或 PDF 檔案) 做為輸入，並解壓縮檔的文字、資料表、選取標記和結構。 呼叫會傳回稱為的回應標頭欄位 `Operation-Location` 。 `Operation-Location`值是包含要在下一個步驟中使用之結果識別碼的 URL。

|回應標頭| 結果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>取得分析版面配置結果作業

第二個步驟是呼叫「 [取得分析版面配置結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) 」運算。 這項作業會以「分析配置」作業所建立的結果識別碼作為輸入。 它會傳回 JSON 回應，其中包含具有下列可能值的 **狀態** 欄位。 

|欄位| 類型 | 可能值 |
|:-----|:----:|:----|
|status | 字串 | `notStarted`：分析作業尚未啟動。<br /><br />`running`：分析作業正在進行中。<br /><br />`failed`：分析作業失敗。<br /><br />`succeeded`：分析作業已成功。|

您會反復呼叫此操作，直到它傳回 `succeeded` 值為止。 使用3到5秒的間隔，以避免每秒超過要求 (RPS) 速率。

當 **狀態** 欄位具有值時 `succeeded` ，JSON 回應會包含已解壓縮的版面配置解壓縮結果、文字、資料表和選取標記。 解壓縮的資料包含已解壓縮的文字行和文字、周框方塊、文字外觀手寫指示、資料表，以及表示選取/取消選取的選取專案標記。 

### <a name="sample-json-output"></a>範例 JSON 輸出

「取得分析」配置結果作業的回應將會是檔的結構化標記法，其中包含所有已解壓縮的資訊。 請參閱這裡以取得 [範例檔](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) 檔和其結構化輸出 [範例版面配置輸出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)。

JSON 輸出有兩個部分： 
* `"readResults"` 節點包含所有已辨識的文字和選取專案標記。 文字會依頁面彙整，然後依文字行，再依個別字組彙整。 
* `"pageResults"` 節點包含使用其周框方塊、可信度和 "readResults" 中的行和單字的參考所解壓縮的資料表和資料格。

## <a name="example-output"></a>範例輸出

### <a name="text"></a>文字

版面配置會將檔中的文字解壓縮 (PDF、TIFF) 和影像 (jpg、png、bmp) 具有不同的文字角度、色彩、角度、檔的相片、傳真、印刷、手寫 (僅限英文) 和混合模式。 文字會以線條、文字、周框方塊、信賴分數和樣式 (手寫或其他) 的資訊進行解壓縮。 所有文字資訊都包含在 `"readResults"` JSON 輸出的區段中。 

### <a name="tables"></a>資料表

版面配置會將檔中的資料表解壓縮 (PDF、TIFF) 和影像 (jpg、png、bmp) 。 檔可以掃描、拍攝或數位化。 資料表可以是具有合併資料格或資料行、具有或不含框線和奇數角度的複雜資料表。 已解壓縮的資料表包括資料行和資料列的數目、資料列範圍和資料行範圍。 每個資料格都會以其周框方塊和區段中所解壓縮之文字的參考進行解壓縮 `"readResults"` 。 資料表資訊位於 `"pageResults"` JSON 輸出的區段中。 

![資料表範例](./media/tables-example.jpg)

### <a name="selection-marks"></a>選取標記

版面配置也會從檔中解壓縮選取專案標記。 已解壓縮的選取標記包括周框方塊、信賴度和狀態 (選取/未選取的) 。 在 JSON 輸出的區段中，會解壓縮選取標記資訊 `"readResults"` 。 

## <a name="next-steps"></a>後續步驟

- 使用[表單辨識器範例 UI](https://fott-preview.azurewebsites.net/)來嘗試您自己的版面配置解壓縮
- 完成 [表單辨識器用戶端程式庫快速入門](quickstarts/client-library.md) ，開始以您選擇的語言來解壓縮版面配置。
- 或者，遵循「 [解壓縮版面配置資料](./QuickStarts/python-layout.md) 」快速入門，使用 Python 和 REST API 來執行版面配置資料解壓縮。

## <a name="see-also"></a>請參閱

* [什麼是表單辨識器？](./overview.md)
* [REST API 參考檔](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)