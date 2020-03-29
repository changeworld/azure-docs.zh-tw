---
title: 導入文檔格式指南 - QnA 製造商
description: 瞭解如何使用 URL 類型導入和創建 QnA 集。
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651989"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>導入文檔和 URL 的格式指南

查看這些格式設置指南，以獲得內容的最佳結果。

## <a name="formatting-considerations"></a>格式化考量

導入檔或 URL 後，QnA Maker 會以[標記格式](https://en.wikipedia.org/wiki/Markdown)轉換和存儲您的內容。 轉換過程在文本中添加新行，如`\n\n`。 瞭解標記格式可説明您瞭解轉換後的內容並管理知識庫內容。

如果直接在知識庫中添加或編輯內容，請使用**標記格式**創建豐富的文本內容或更改答案中已有的標記格式內容。 QnA Maker 支援大部分標記格式，為您的內容帶來豐富的文本功能。 但是，用戶端應用程式（如聊天機器人）可能不支援相同的標記格式集。 測試用戶端應用程式的顯示答案非常重要。

## <a name="basic-document-formatting"></a>基本文檔格式

QnA Maker 根據視覺線索（如：

* 字型大小
* 字體樣式
* 編號
* 色彩

|文件範例|
|--|
||



## <a name="product-manuals"></a>產品手冊

手冊通常式隨附於產品的指引內容。 它可協助使用者設定、使用、維護產品，並對其問題進行疑難排解。 當 QnA Maker 處理手冊時，它會將標題和子標題擷取為問題，並將後續的內容擷取為解答。 請參閱[這裡](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的範例。

以下是具有索引頁面與階層式內容的手冊範例

 ![知識庫的產品手冊範例](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 手冊若具有目錄及/或索引頁面，和包含階層式標題的明確結構，擷取將會有最佳效果。

## <a name="brochures-guidelines-papers-and-other-files"></a>摺頁冊、指導方針、白皮書及其他檔案

許多其他類型的文件經過處理後也能產生問答組合，但前提是這些文件要有明確的結構和版面配置。 其中包括：摺頁冊、指南、報告、白皮書、科學論文、政策、書籍等。[在此處](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)查看示例。

以下是沒有索引的半結構化文件範例：

 ![Azure Blob 儲存體的半結構化文件](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>結構化 QnA 文件

DOC 檔中結構化問答的格式，會採用逐行交替列出問題和解答的形式，每行一個問題，接著的下一行則是其解答，如下所示：

```text
Question1

Answer1

Question2

Answer2
```

以下是結構化 QnA Word 文件的範例：

 ![知識庫的結構化 QnA 文件範例](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>結構化的 TXT**、TSV** 和 XLS** 檔案

採用結構化 .txt**、.tsv** 或 .xls** 檔案形式的 QnA 也可上傳至 QnA Maker，以便建立或擴大知識庫。  這些檔案可以是純文字，也可以具有 RTF 或 HTML 的內容。

| 問題  | Answer  | 中繼資料（1 個鍵：1 個值） |
|-----------|---------|-------------------------|
| 問題 1 | 解答 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 |      `Key:Value`           |

來源檔案中的任何其他資料行都會被忽略。

### <a name="example-of-structured-excel-file"></a>結構化 Excel 檔的示例

以下是具有 HTML 內容的結構化 QnA .xls** 檔案範例：

 ![知識庫的結構化 QnA Excel 範例](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 檔中單一答案的備用問題示例

下面是結構化 QnA *.xls*檔的示例，其中有幾個備用問題可供單個答案回答：

 ![Excel 檔中單一答案的備用問題示例](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

導入檔後，問答對位於知識庫中，如下所示：

 ![導入知識庫的單一答案的備用問題的螢幕截圖](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>透過匯入的結構化資料格式

匯入知識庫後，將會取代現有知識庫的內容。 匯入時必須要有包含資料來源資訊的結構化 .tsv 檔案。 這項資訊可協助 QnA Maker 將問答組分組，並將其歸於特定資料來源。

| 問題  | Answer  | 來源| 中繼資料（1 個鍵：1 個值） |
|-----------|---------|----|---------------------|
| 問題 1 | 解答 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 | 編輯|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>多轉文檔格式

* 使用標題和子標題展示層次結構。 例如，您可以 h1 表示父 QnA 和 h2 以表示應作為提示符的 QnA。 使用小標題大小表示後續層次結構。 不要使用樣式、顏色或其他機制來暗示文檔中的結構，QnA Maker 不會提取多轉提示。
* 標題的第一個字元必須大寫。
* 不要用問號結束標題。 `?`


|文件範例|
|--|
||