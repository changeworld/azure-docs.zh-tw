---
title: 匯入檔案格式方針-QnA Maker
description: 瞭解 Url 類型如何用來匯入和建立 QnA 組。
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 799e85e2200d3caa29c9b76bc57a62fc883d246d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804328"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>匯入檔和 Url 的格式方針

請參閱這些格式設定指導方針，以取得您內容的最佳結果。

## <a name="formatting-considerations"></a>格式化考量

匯入檔案或 URL 之後，QnA Maker 會以[markdown 格式](https://en.wikipedia.org/wiki/Markdown)轉換並儲存您的內容。 轉換程式會在文字中加入新行，例如`\n\n`。 Markdown 格式的知識可協助您瞭解已轉換的內容，並管理您的知識庫內容。

如果您直接在知識庫中新增或編輯內容，請使用**markdown 格式**來建立 rtf 文字內容，或變更已在解答中的 markdown 格式內容。 QnA Maker 支援許多 markdown 格式，可將豐富的文字功能帶入您的內容。 不過，用戶端應用程式（例如聊天機器人）可能不支援相同的一組 markdown 格式。 測試用戶端應用程式的答案顯示是很重要的。

如需[內容類型和範例](./Concepts/content-types.md#file-and-url-data-types)的完整清單，請參閱。

## <a name="basic-document-formatting"></a>基本檔案格式

QnA Maker 會根據視覺線索，識別檔案中的區段和小節和關聯性，例如：

* 字型大小
* 字型樣式
* 低
* 色彩

## <a name="product-manuals"></a>產品手冊

手冊通常式隨附於產品的指引內容。 它可協助使用者設定、使用、維護產品，並對其問題進行疑難排解。 當 QnA Maker 處理手冊時，它會將標題和子標題擷取為問題，並將後續的內容擷取為解答。 請參閱[這裡](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的範例。

以下是具有索引頁面與階層式內容的手冊範例

 ![知識庫的產品手冊範例](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 手冊若具有目錄及/或索引頁面，和包含階層式標題的明確結構，擷取將會有最佳效果。

## <a name="brochures-guidelines-papers-and-other-files"></a>摺頁冊、指導方針、白皮書及其他檔案

許多其他類型的文件經過處理後也能產生問答組合，但前提是這些文件要有明確的結構和版面配置。 其中包括：摺頁冊、指導方針、報告、白皮書、科學論文、原則、書籍等。請參閱[這裡](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)的範例。

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

| 問題  | Answer  | 中繼資料（1個索引鍵：1個值） |
|-----------|---------|-------------------------|
| 問題 1 | 解答 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 |      `Key:Value`           |

來源檔案中的任何其他資料行都會被忽略。

### <a name="example-of-structured-excel-file"></a>結構化 Excel 檔案的範例

以下是具有 HTML 內容的結構化 QnA .xls** 檔案範例：

 ![知識庫的結構化 QnA Excel 範例](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 檔案中單一答案的替代問題範例

以下是結構化 QnA *.xls*檔案的範例，其中單一答案有數個替代問題：

 ![Excel 檔案中單一答案的替代問題範例](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

匯入檔案之後，問與答配對會在知識庫中，如下所示：

 ![匯入知識庫之單一回應的替代問題螢幕擷取畫面](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>透過匯入的結構化資料格式

匯入知識庫後，將會取代現有知識庫的內容。 匯入時必須要有包含資料來源資訊的結構化 .tsv 檔案。 這項資訊可協助 QnA Maker 將問答組分組，並將其歸於特定資料來源。

| 問題  | Answer  | 來源| 中繼資料（1個索引鍵：1個值） |
|-----------|---------|----|---------------------|
| 問題 1 | 解答 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 | 編輯|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>多回合檔案格式設定

* 使用標題和子標題來表示階層。 例如，您可以利用 h1 來表示父 QnA 和 h2，以表示應該當做提示的 QnA。 使用 [小標題大小] 來表示後續的階層。 請勿使用樣式、色彩或其他一些機制來表示檔中的結構，QnA Maker 不會解壓縮多回合提示。
* 標題的第一個字元必須是大寫。
* 不要結束具有問號的標題`?`。

**範例檔**：<br>[Surface Pro （.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso 權益（.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso 權益（pdf）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>後續步驟

請參閱[內容類型和範例](./Concepts/content-types.md#file-and-url-data-types)的完整清單