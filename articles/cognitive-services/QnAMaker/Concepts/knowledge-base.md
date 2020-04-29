---
title: 從資料來源匯入-QnA Maker
description: QnA Maker 知識庫是由一組問答集（QnA）集以及與每個 QnA 配對相關聯的選用中繼資料所組成。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804277"
---
# <a name="importing-from-data-sources"></a>從資料來源匯入

知識庫是由公用 Url 和檔案引入的問題和答案集所組成。

## <a name="data-source-locations"></a>資料來源位置

內容會從資料來源帶入知識庫中。 資料來源位置是**公用 url 或**檔案，不需要驗證。

使用驗證保護的[SharePoint](../how-to/add-sharepoint-datasources.md)檔案是例外狀況。 SharePoint 資源必須是檔案，而不是網頁。 如果 URL 的結尾是 web 延伸模組，例如。ASPX，它不會從 SharePoint 匯入 QnA Maker。

## <a name="chit-chat-content"></a>閒聊聊天內容

閒聊 chat QnA 內容集是以數種語言和交談樣式提供的完整內容資料來源。 這可以是您 Bot 的特質起始點，並將節省您從頭撰寫它們的時間和成本。 瞭解[如何將](../how-to/chit-chat-knowledge-base.md)此內容集新增至您的知識庫。

## <a name="structured-data-format-through-import"></a>透過匯入的結構化資料格式

匯入知識庫後，將會取代現有知識庫的內容。 匯入需要包含`.tsv`問題和答案的結構化檔案。 此資訊可協助 QnA Maker 將問答集分組，並將其屬性設定為特定的資料來源。

| 問題  | Answer  | 來源| 中繼資料（1個索引鍵：1個值） |
|-----------|---------|----|---------------------|
| 問題 1 | 解答 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 | 編輯|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>透過匯入的結構化多回合格式

您可以使用`.tsv`檔案格式來建立多回合交談。 此格式可讓您藉由分析先前的聊天記錄（與其他進程，而不是使用 QnA Maker）來建立多回合交談，然後透過自動化`.tsv`建立檔案。 匯入檔案以取代現有的知識庫。

> [!div class="mx-imgBorder"]
> ![3個多回合問題層級的概念模型](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

多回合的特定資料行`.tsv`會**提示**。 範例`.tsv`（顯示在 Excel 中）顯示要包含的資訊，以定義多回合子系：

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder**是數值，**而文字類型則是不應**包含 markdown 的文字。

> [!div class="mx-imgBorder"]
> ![如 Excel 所示的多回合問題範例](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>匯出為範例

如果您不確定如何在檔案中代表您的`.tsv` QnA 組：
* [從 GitHub 使用此可下載的範例](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* 或者，在 QnA Maker 入口網站中建立集合、儲存，然後匯出知識庫，以取得如何呈現該集合的範例。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [知識庫的開發生命週期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>請參閱

使用 QnA Maker 的[Markdown 參考](../reference-markdown-format.md)，協助您設定答案的格式。

[QnA Maker 概觀](../Overview/overview.md)

使用下列方式建立和編輯知識庫：
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

使用下列程式產生答案：
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
