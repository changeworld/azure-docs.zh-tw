---
title: 匯入檔案格式指導方針-QnA Maker
description: 您可以使用這些指導方針來匯入檔，以取得內容的最佳結果。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: d35ea57a68e500deffa99033b83114ec518dd5e3
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776726"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>匯入檔和 Url 的格式指導方針

請參閱這些格式設定指導方針，以取得內容的最佳結果。

## <a name="formatting-considerations"></a>格式化考量

匯入檔案或 URL 之後，QnA Maker 會以 [markdown 格式](https://en.wikipedia.org/wiki/Markdown)轉換和儲存您的內容。 轉換程式會在文字中加入新行，例如 `\n\n` 。 Markdown 格式的知識可協助您瞭解已轉換的內容，並管理您的知識庫內容。

如果您直接在知識庫中新增或編輯內容，請使用 **markdown 格式** 來建立 rich text 內容，或變更已在答案中的 markdown 格式內容。 QnA Maker 支援大部分的 markdown 格式，可將豐富的文字功能帶到您的內容。 不過，用戶端應用程式（例如聊天機器人）可能不支援一組相同的 markdown 格式。 測試用戶端應用程式的答案顯示是很重要的。

查看 [內容類型與範例](./Concepts/content-types.md#file-and-url-data-types)的完整清單。

## <a name="basic-document-formatting"></a>基本檔案格式設定

QnA Maker 會根據視覺線索來識別檔案中的區段和子區段和關聯性，例如：

* 字型大小
* 字型樣式
* 編號
* 色彩

## <a name="product-manuals"></a>產品手冊

手冊通常式隨附於產品的指引內容。 它可協助使用者設定、使用、維護產品，並對其問題進行疑難排解。 當 QnA Maker 處理手冊時，它會將標題和子標題擷取為問題，並將後續的內容擷取為解答。 請參閱[這裡](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的範例。

以下是具有索引頁面與階層式內容的手冊範例

 ![知識庫的產品手冊範例](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 手冊若具有目錄及/或索引頁面，和包含階層式標題的明確結構，擷取將會有最佳效果。

## <a name="brochures-guidelines-papers-and-other-files"></a>摺頁冊、指導方針、白皮書及其他檔案

許多其他類型的文件經過處理後也能產生問答組合，但前提是這些文件要有明確的結構和版面配置。 這些包括：手冊、指導方針、報告、白皮書、科學論文、原則、書籍等。請參閱 [這裡](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)的範例。

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

| 問題  | Answer  | 中繼資料 (1 索引鍵：1值)  |
|-----------|---------|-------------------------|
| 問題 1 | 解答 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 |      `Key:Value`           |

來源檔案中的任何其他資料行都會被忽略。

### <a name="example-of-structured-excel-file"></a>結構化 Excel 檔案的範例

以下是具有 HTML 內容的結構化 QnA .xls** 檔案範例：

 ![知識庫的結構化 QnA Excel 範例](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 檔案中單一答案的替代問題範例

以下是結構化 QnA *.xls* 檔案的範例，其中有幾個替代問題適用于單一答案：

 ![Excel 檔案中單一答案的替代問題範例](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

匯入檔案之後，問與答組會在知識庫中，如下所示：

 ![將單一回應匯入知識庫的替代問題螢幕擷取畫面](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>透過匯入的結構化資料格式

匯入知識庫後，將會取代現有知識庫的內容。 匯入時必須要有包含資料來源資訊的結構化 .tsv 檔案。 這項資訊可協助 QnA Maker 將問答組分組，並將其歸於特定資料來源。

| 問題  | Answer  | 來源| 中繼資料 (1 索引鍵：1值)  |
|-----------|---------|----|---------------------|
| 問題 1 | 解答 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 | 編輯|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>多回合檔案格式

* 使用標題和子標題表示階層。 例如，您可以用 h1 來表示父 QnA 和 h2，以代表應視為提示的 QnA。 使用小標題大小來表示後續階層。 請勿使用樣式、色彩或其他某些機制來暗示檔中的結構，QnA Maker 將不會解壓縮多回合提示。
* 標題的第一個字元必須為大寫。
* 請勿以問號結尾的標題結尾 `?` 。

**範例檔**：<br>[Surface Pro (.docx) ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso 權益 (.docx) ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso 權益 (pdf) ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>後續步驟

查看[內容類型與範例](./Concepts/content-types.md#file-and-url-data-types)的完整清單