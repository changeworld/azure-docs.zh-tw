---
title: 資料來源和內容類型-QnA Maker
description: 瞭解如何從資料來源和支援的內容類型（包括許多標準結構化檔，例如 PDF、.DOCX 和 TXT QnA Maker）匯入問題和答案配對。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: a6488d2c7611513528ff02d4e620124763912730
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353250"
---
# <a name="importing-from-data-sources"></a>從資料來源匯入

知識庫是由公開 Url 和檔案所提供的問題和答案配對所組成。

## <a name="data-source-locations"></a>資料來源位置

從資料來源將內容帶入知識庫。 資料來源位置是不需要驗證的 **公用 url 或** 檔案。

以驗證保護的[SharePoint](../how-to/add-sharepoint-datasources.md)檔案是例外狀況。 SharePoint 資源必須是檔案，而不是網頁。 URL 的結尾若是 Web 延伸模組 (例如 .ASPX)，則不會從 SharePoint 匯入 QnA Maker 中。

## <a name="chit-chat-content"></a>閒聊-聊天內容

閒聊聊天內容集是以數種語言和交談式樣式的完整內容資料來源來提供。 這可以是您 Bot 的特質起始點，並將節省您從頭撰寫它們的時間和成本。 瞭解 [如何將閒聊交談內容新增](../how-to/chit-chat-knowledge-base.md) 至您的知識庫。

## <a name="structured-data-format-through-import"></a>透過匯入的結構化資料格式

匯入知識庫後，將會取代現有知識庫的內容。 匯入需要 `.tsv` 包含問題和答案的結構化檔案。 這項資訊可協助 QnA Maker 將問答組分組，並將其歸於特定資料來源。

| 問題  | Answer  | 來源| 中繼資料 (1 索引鍵：1值)  |
|-----------|---------|----|---------------------|
| 問題 1 | 解答 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 | 編輯|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>透過匯入的結構化多重回合格式

您可以使用檔案格式來建立多回合交談 `.tsv` 。 此格式可讓您藉由分析先前的聊天記錄 (與其他程式，而不是使用 QnA Maker) ，然後透過自動化建立檔案，藉此建立多回合對話 `.tsv` 。 匯入檔案以取代現有的知識庫。

> [!div class="mx-imgBorder"]
> ![3個層級的多重回合問題的概念模型](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

多回合專用的資料行 `.tsv` 會 **提示**。 Excel 中顯示的範例會 `.tsv` 顯示要包含的資訊，以定義多回合子系：

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** 是數值，**而文字內容則是不** 應包含 markdown 的文字。

> [!div class="mx-imgBorder"]
> ![Excel 中顯示的多重回合問題範例](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>匯出為範例

如果您不確定如何在檔案中代表 QnA 配對 `.tsv` ：
* [從 GitHub 使用此可下載的範例](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* 或者，在 QnA Maker 入口網站中建立配對，儲存，然後匯出知識庫，以取得如何表示配對的範例。

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>您可以新增至知識庫的檔內容類型
內容類型包含許多標準的結構化檔，例如 PDF、DOC 和 TXT。

### <a name="file-and-url-data-types"></a>檔案和 URL 資料類型

下表摘要說明 QnA Maker 所支援的內容類型和檔案格式。

|來源類型|內容類型| 範例|
|--|--|--|
|URL|常見問題集<br> (一般，含有區段或含有主題首頁)<br>支援頁面 <br> (單頁操作說明文章、疑難排解文章等等。)|[一般的常見問題集](../troubleshooting.md)、 <br>[具有連結的常見問題集](https://www.microsoft.com/en-us/software-download/faq)、<br> [具有主題首頁的常見問題集](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支援文件](./best-practices.md)|
|PDF/DOC|常見問題集、<br> 產品手冊、<br> 小手冊、<br> 文件、<br> 傳單原則、<br> 支援指南、<br> 結構化 QnA<br> 等等。|**無多回合**<br>[結構化 QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [範例 semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [範例白色 paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)、<br><br>**多回合**：<br>[Surface Pro (.docx) ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso 權益 (.docx) ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso 權益 (pdf) ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|結構化 QnA 檔案<br> (包括 RTF、HTML 支援)|**沒有多回合**：<br>[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**多回合**：<br>[結構化簡單 FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface 膝上型電腦 FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|結構化 QnA 檔案|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

如果您需要對資料來源進行驗證，請考慮下列方法，以取得 QnA Maker 的內容：

* 手動下載檔案並匯入 QnA Maker
* 從已驗證的[Sharepoint 位置](../how-to/add-sharepoint-datasources.md)新增檔案

### <a name="url-content"></a>URL 內容

您可以透過 QnA Maker 中的 **URL** 匯入兩種類型的檔：

* 常見問題集 URL
* 支援 URL

每個類型都表示預期的格式。

### <a name="file-based-content"></a>以檔案為基礎的內容

您可以在 [QnA Maker 入口網站](https://www.qnamaker.ai)中，從公用來源或本機檔案系統將檔案新增至知識庫。

### <a name="content-format-guidelines"></a>內容格式指導方針

深入瞭解不同檔案的 [格式指導方針](../reference-document-format-guidelines.md) 。

## <a name="next-steps"></a>後續步驟

瞭解 [ (QnA) 組的問題和解答](question-answer-set.md)中儲存的資訊。