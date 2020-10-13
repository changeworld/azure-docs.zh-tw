---
title: 從資料來源匯入-QnA Maker
description: 瞭解如何從資料來源匯入問題和答案配對-QnA Maker。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5f609dc27aa1251cfad0249d26ef5140936bfe41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776947"
---
# <a name="importing-from-data-sources"></a>從資料來源匯入

知識庫是由公開 Url 和檔案所提供的問題和答案配對所組成。

## <a name="data-source-locations"></a>資料來源位置

從資料來源將內容帶入知識庫。 資料來源位置是不需要驗證的 **公用 url 或**檔案。

以驗證保護的[SharePoint](../how-to/add-sharepoint-datasources.md)檔案是例外狀況。 SharePoint 資源必須是檔案，而不是網頁。 URL 的結尾若是 Web 延伸模組 (例如 .ASPX)，則不會從 SharePoint 匯入 QnA Maker 中。

## <a name="chit-chat-content"></a>閒聊聊天內容

閒聊 chat QnA 內容集是以數種語言和對話樣式的完整內容資料來源來提供。 這可以是您 Bot 的特質起始點，並將節省您從頭撰寫它們的時間和成本。 瞭解 [如何將](../how-to/chit-chat-knowledge-base.md) 此內容集新增至您的知識庫。

## <a name="structured-data-format-through-import"></a>透過匯入的結構化資料格式

匯入知識庫後，將會取代現有知識庫的內容。 匯入需要 `.tsv` 包含問題和解答的結構化檔案。 這項資訊可協助 QnA Maker 將問答組分組，並將其歸於特定資料來源。

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

**DisplayOrder**是數值，**而文字內容則是不**應包含 markdown 的文字。

> [!div class="mx-imgBorder"]
> ![Excel 中顯示的多重回合問題範例](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>匯出為範例

如果您不確定如何在檔案中代表 QnA 配對 `.tsv` ：
* [從 GitHub 使用此可下載的範例](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* 或者，在 QnA Maker 入口網站中建立配對，儲存，然後匯出知識庫，以取得如何表示配對的範例。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [知識庫的開發生命週期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>另請參閱

使用 QnA Maker [Markdown 參考](../reference-markdown-format.md) 可協助您格式化您的答案。

[QnA Maker 概觀](../Overview/overview.md)

使用下列方式建立和編輯知識庫：
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

使用下列內容產生答案：
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
