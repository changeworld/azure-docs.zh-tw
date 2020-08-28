---
title: 將筆跡資料傳送至筆跡辨識器 API
titleSuffix: Azure Cognitive Services
description: 了解如何為不同應用程式呼叫筆跡分析器 API
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: aahi
ms.openlocfilehash: 8665e27b6b7c43bce7d749d9bfe0a2f815a0f46d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051095"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>將筆跡資料傳送至筆跡辨識器 API 

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

數位筆跡是指能夠數位呈現手寫和繪圖等輸入的技術。 要實現此功能，我們一般會使用數位板來擷取手寫筆等輸入裝置的移動。 由於裝置能夠繼續提供豐富的數位筆跡體驗，因此人工智慧和機器學習能夠辨識任何內容中的書寫圖形和文字。 筆跡辨識器 API 可讓您傳送筆跡筆觸並取得其相關詳細資訊。 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>筆跡辨識器 API 與 OCR 服務

筆跡辨識器 API 不會使用光學字元辨識 (OCR)。 OCR 服務會處理影像中的像素資料，以提供手寫和文字辨識。 這有時候稱為離線辨識。 相反地，筆跡辨識器 API 則需要使用輸入裝置時所擷取的數位筆跡筆觸資料。 相較於 OCR 服務，以這種方式處理數位筆跡資料會產生更精確的辨識結果。 

## <a name="sending-ink-data"></a>傳送筆跡資料

筆跡辨識器 API 需要以 X 和 Y 座標來代表輸入裝置所建立的筆跡筆觸 (從碰觸到偵測面到拿起時)。 每個筆觸點都必須是逗點分隔值所形成的字串，而且必須使用 JSON 格式，如下列範例所示。 此外，每個筆跡筆觸也必須在每個要求中具有唯一的識別碼。 如果相同的要求中有重複的識別碼，API 就會傳回錯誤。 為了獲得最精確的辨識結果，小數點後請至少要有 8 個數字。 我們假設畫布的原點 (0,0) 在筆跡畫布的最左上角。

> [!NOTE]
> 下列範例不是有效的 JSON。 您可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909) 上找到完整的筆跡辨識器 JSON 要求。
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>筆跡辨識器回應

筆跡辨識器 API 會針對其從筆跡內容所辨識出的物件，傳回該物件的相關分析回應。 回應中會包含可描述不同筆跡筆觸之間關聯性的辨識單位。 例如，會建立截然不同圖形的筆觸將會包含在不同的單位內。 每個單位都會包含其筆跡筆觸的詳細資訊，包括可辨識的物件、其座標和其他繪圖屬性。

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>筆跡辨識器 API 所辨識的圖形

筆跡辨識器 API 可以識別筆記記錄中最常使用的圖形。 下圖顯示一些基本範例。 如需該 API 所辨識圖形和其他筆跡內容的完整清單，請參閱 [API 參考文章](https://go.microsoft.com/fwlink/?linkid=2089907)。 

![筆跡辨識器 API 所辨識圖形的清單](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>建議的呼叫模式

您可以根據應用程式以不同模式呼叫筆跡辨識器 REST API。 

### <a name="user-initiated-api-calls"></a>使用者起始的 API 呼叫

如果您要建置會取得使用者輸入 (例如，筆記記錄或註釋應用程式) 的應用程式，請讓其能夠控制要於何時將哪些筆跡傳送給 筆跡辨識器 API。 當畫布上同時出現文字和圖形，且使用者想要分別為其執行不同動作時，這項功能會特別有用。 請考慮新增選取功能 (例如，套索或其他幾何選取工具)，讓使用者能夠選擇要將哪些項目傳送給 API。  

### <a name="app-initiated-api-calls"></a>應用程式起始的 API 呼叫

您也可以讓應用程式在逾時後呼叫筆跡辨識器 API。 藉由定期傳送目前的筆跡筆觸給 API，您可以儲存所建立的辨識結果，同時改善 API 的回應時間。 例如，您可以在偵測到使用者已寫完一行文字後，將該行文字傳送給 API。 

事先獲得辨識結果可讓您在筆跡筆觸彼此相關時，獲得其特徵的相關資訊。 例如，哪些筆觸會形成群組以形成相同的字組、線條、清單、段落或圖形。 這項資訊由於能夠讓您一次選取數個群組的筆觸 (舉例來說)，所以能夠增強應用程式的筆跡選取功能。

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>整合筆跡辨識器 API 與 Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) 提供各種工具和技術，因此能在各種裝置上實現數位筆跡體驗。 您可以將 Windows Ink 平臺與筆跡辨識器 API 結合，以建立顯示及解讀數位筆跡筆劃的應用程式。

## <a name="next-steps"></a>後續步驟

* [什麼是筆跡辨識器 API？](../overview.md)
* [筆跡辨識器 REST API 參考](https://go.microsoft.com/fwlink/?linkid=2089907)

* 使用下列工具來開始傳送數位筆跡筆觸資料：
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)
