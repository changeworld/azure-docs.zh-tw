---
title: 內容類型-QnA Maker
description: 內容類型包含許多標準結構化檔，例如 PDF、DOC 和 TXT。
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 3020c0623cff73e5a907f4d63f08f5866afd52dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902084"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>可新增至知識庫的檔內容類型
內容類型包含許多標準結構化檔，例如 PDF、DOC 和 TXT。

## <a name="file-and-url-data-types"></a>檔案和 URL 資料類型

下表摘要說明 QnA Maker 所支援的內容類型和檔案格式。

|來源類型|內容類型| 範例|
|--|--|--|
|URL|常見問題集<br> (一般，含有區段或含有主題首頁)<br>支援頁面 <br> (單頁操作說明文章、疑難排解文章等等。)|[一般的常見問題集](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)、 <br>[具有連結的常見問題集](https://www.microsoft.com/en-us/software-download/faq)、<br> [具有主題首頁的常見問題集](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支援文件](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|常見問題集、<br> 產品手冊、<br> 小手冊、<br> 文件、<br> 傳單原則、<br> 支援指南、<br> 結構化 QnA<br> 等|**無多回合**<br>[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [範例白皮書 .pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)、<br><br>**多回合**：<br>[Surface Pro （.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso 權益（.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso 權益（pdf）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|\* Excel|結構化 QnA 檔案<br> (包括 RTF、HTML 支援)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|結構化 QnA 檔案|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

如果您需要驗證資料來源，請考慮下列方法，將該內容匯入 QnA Maker：

* 手動下載檔案並匯入 QnA Maker
* 從已驗證的[Sharepoint 位置](../how-to/add-sharepoint-datasources.md)新增檔案

## <a name="url-content"></a>URL 內容

您可以透過 QnA Maker 中的**URL**匯入兩種檔案類型：

* 常見問題集 URL
* 支援 Url

每種類型都表示預期的格式。

## <a name="file-based-content"></a>以檔案為基礎的內容

您可以在[QnA Maker 入口網站](https://www.qnamaker.ai)中，從公用來源或本機檔案系統將檔案新增至知識庫。

## <a name="content-format-guidelines"></a>內容格式方針

QnA Maker 新增檔案和 URL 內容，並將內容轉換成 QnA 集，並儲存為 markdown （`.md`）。 內容適用于知識庫，其中內容是以某種結構化形式組織，並以妥善定義的區段表示。 這些區段可以進一步細分為小節或子主題。 內容若具有包含階層式標題的明確結構，擷取將會有最佳效果。

QnA Maker 根據視覺線索（如字型大小、字型樣式、編號、色彩等）來識別內容中的區段和子章節和關聯性。半結構化內容包括手冊、常見問題、指導方針、原則、摺頁冊、海報，以及許多其他類型的檔。

## <a name="faq-urls"></a>常見問題集 URL

QnA Maker 可支援 3 種不同形式的常見問題集網頁：一般常見問題集網頁、具有連結的常見問題集網頁、具有主題首頁的常見問題集網頁。


### <a name="plain-faq-pages"></a>一般的常見問題集頁面

這是最常用的常見問題集頁面類型，在此處，解答會在相同的頁面中緊接在問題後面。

以下是一般常見問題集網頁的範例：

![知識庫的一般常見問題集頁面範例](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>具有連結的常見問題集網頁

在這種常見問題集網頁中，問題會彙總在一起並連結至解答 (位於相同網頁的不同區段，或位於不同網頁)。

以下是連結位於相同網頁區段的常見問題集網頁範例：

 ![知識庫的區段連結常見問題集頁面範例](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>具有主題首頁的常見問題集網頁

這種常見問題集會有首頁，首頁會有主題，且每個主題都是其相關 QnA 在不同網頁的連結。 在此，QnA Maker 會搜耙所有連結頁面，以擷取對應的問題和解答。

以下是主題首頁上有不同網頁常見問題集區段連結的常見問題集網頁範例。

 ![知識庫的深層連結常見問題集頁面範例](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>支援 URL

QnA Maker 可處理半結構化的支援網頁，例如 Web 文件，其中會說明如何執行指定工作、如何診斷及解決特定問題，以及指定程序的最佳做法是什麼。 內容若具有包含階層式標題的明確結構，擷取將會有最佳效果。

> [!NOTE]
> 擷取支援文章是一項新功能，並且處於早期階段。 其適合用於結構完整且不包含複雜標頭/頁尾的簡單頁面。

![QnA Maker 支援從半結構化的網頁進行擷取，其中會以階層式標題顯示清楚的結構](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF/DOC 檔

QnA Maker 可以處理 PDF 或 DOC 檔中的半結構化內容，並將它轉換成 QnA。 可以完好擷取的良好檔案，其內容會組織成某種結構化形式，並會以妥善定義的區段來表示。 區段可以再分成子區段或子主題。 文件若具有包含階層式標題的明確結構，擷取將會有最佳效果。

QnA Maker 會根據視覺線索（如字型大小、字型樣式、編號、色彩等），識別檔案中的區段和小節和關聯性。半結構化 PDF 或檔檔案可能是手冊、常見問題、指導方針、原則、摺頁冊、海報和許多其他類型的檔案。 以下是這些檔案類型的一些範例。

### <a name="product-manuals"></a>產品手冊

手冊通常式隨附於產品的指引內容。 它可協助使用者設定、使用、維護產品，並對其問題進行疑難排解。 當 QnA Maker 處理手冊時，它會將標題和子標題擷取為問題，並將後續的內容擷取為解答。 請參閱[這裡](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的範例。

以下是具有索引頁面與階層式內容的手冊範例

 ![知識庫的產品手冊範例](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 手冊若具有目錄及/或索引頁面，和包含階層式標題的明確結構，擷取將會有最佳效果。

### <a name="brochures-guidelines-papers-and-other-files"></a>摺頁冊、指導方針、白皮書及其他檔案

許多其他類型的文件經過處理後也能產生問答組合，但前提是這些文件要有明確的結構和版面配置。 其中包括：摺頁冊、指導方針、報告、白皮書、科學論文、原則、書籍等。請參閱[這裡](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)的範例。

以下是沒有索引的半結構化文件範例：

 ![Azure Blob 儲存體的半結構化文件](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>結構化 QnA 文件

DOC 檔中結構化問答的格式，會採用逐行交替列出問題和解答的形式，每行一個問題，接著的下一行則是其解答，如下所示：

```text
Question1

Answer1

Question2

Answer2
```

以下是結構化 QnA Word 文件的範例：

 ![知識庫的結構化 QnA 文件範例](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>結構化的 TXT、TSV 和 XLS 檔案

採用結構化 .txt、.tsv 或 .xls 檔案形式的 QnA 也可上傳至 QnA Maker，以便建立或擴大知識庫。  這些檔案可以是純文字，也可以具有 RTF 或 HTML 的內容。

| 問題  | 答案  | 中繼資料（1個索引鍵：1個值） |
|-----------|---------|-------------------------|
| 問題 1 | 解答 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 |      `Key:Value`           |

來源檔案中的任何其他資料行都會被忽略。

### <a name="example-of-structured-excel-file"></a>結構化 Excel 檔案的範例

以下是具有 HTML 內容的結構化 QnA .xls 檔案範例：

 ![知識庫的結構化 QnA Excel 範例](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 檔案中單一答案的替代問題範例

以下是結構化 QnA *.xls*檔案的範例，其中單一答案有數個替代問題：

 ![Excel 檔案中單一答案的替代問題範例](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

匯入檔案之後，問與答配對會在知識庫中，如下所示：

 ![匯入知識庫之單一回應的替代問題螢幕擷取畫面](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>格式化考量

匯入檔案或 URL 之後，QnA Maker 會以[markdown 格式](https://en.wikipedia.org/wiki/Markdown)轉換並儲存您的內容。 轉換程式會在文字中加入新行，例如 `\n\n`。 Markdown 格式的知識可協助您瞭解已轉換的內容，並管理您的知識庫內容。

如果您直接在知識庫中新增或編輯內容，請使用**markdown 格式**來建立 rtf 文字內容，或變更已在解答中的 markdown 格式內容。 QnA Maker 支援許多 markdown 格式，可將豐富的文字功能帶入您的內容。 不過，用戶端應用程式（例如聊天機器人）可能不支援相同的一組 markdown 格式。 測試用戶端應用程式的答案顯示是很重要的。

若要深入瞭解，請參閱[QnA Maker markdown 參考檔](../reference-markdown-format.md)。

## <a name="testing-your-markdown"></a>測試您的 Markdown

請使用 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** \(英文\) 教學課程來驗證您的 Markdown。 此教學課程具有 [Try it] \(試用\) 功能，可快速進行複製/貼上驗證。

## <a name="next-steps"></a>後續步驟

* 瞭解如何設計和管理[問與答（QnA）集](question-answer-set.md)