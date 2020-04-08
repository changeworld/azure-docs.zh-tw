---
title: 從資料來源匯入 - QnA 製造商
description: QnA Maker 知識庫由一組問答 (QnA) 集和與每個 QnA 對關聯的可選元數據組成。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804277"
---
# <a name="importing-from-data-sources"></a>從資料來源匯入

知識庫由公共 URL 和檔帶來的問答集組成。

## <a name="data-source-locations"></a>資料來源位置

內容從數據源引入知識庫。 數據源位置是**公共 URL 或檔**,不需要身份驗證。

[共用點檔](../how-to/add-sharepoint-datasources.md)(通過身份驗證保護)是例外。 SharePoint 資源必須是檔,而不是網頁。 如果 URL 以 Web 副檔名(如 ) 結尾。ASPX,它將不會從SharePoint導入QnA製造商。

## <a name="chit-chat-content"></a>Chit 聊天內容

Chit 聊天 QnA 內容集以多種語言和會話樣式作為完整的內容數據源提供。 這可以是您 Bot 的特質起始點，並將節省您從頭撰寫它們的時間和成本。 [瞭解如何將](../how-to/chit-chat-knowledge-base.md)此內容集添加到知識庫。

## <a name="structured-data-format-through-import"></a>透過匯入的結構化資料格式

匯入知識庫後，將會取代現有知識庫的內容。 導入需要包含問題和`.tsv`答案的結構化檔。 此資訊可説明 QnA Maker 對問題答案集進行分組,並將其歸為特定的數據源。

| 問題  | Answer  | 來源| 中繼資料(1個鍵:1 個值) |
|-----------|---------|----|---------------------|
| 問題 1 | 解答 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 | 編輯|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>以匯入編譯多轉格式

您可以以`.tsv`檔案格式建立多轉對話。 該格式使您能夠透過分析以前的聊天日誌(與其他行程一起,不使用 QnA Maker)創建多圈對話,然後透過`.tsv`自動化創建檔案。 導入檔以替換現有知識庫。

> [!div class="mx-imgBorder"]
> ![3級多轉問題的概念模型](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

多轉`.tsv`的列,特定於多轉是 **"提示**"。 Excel`.tsv`顯示的範例顯示要包含用於定義多轉子級的資訊:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**顯示順序**是數字,**顯示文字**是不應包含標記的文本。

> [!div class="mx-imgBorder"]
> ![多轉題示例,如 Excel 所示](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>匯出為範例

如果您不確定如何在`.tsv`檔案中QnA 對:
* 使用[GitHub 的此可下載範例](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* 或者在 QnA Maker 門戶中創建集,保存,然後匯出知識庫,以瞭解如何表示集的示例。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [知識庫的開發生命週期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>另請參閱

使用 QnA 製造商[標記標記引用](../reference-markdown-format.md)可説明您設定答案的格式。

[QnA Maker 概觀](../Overview/overview.md)

建立與編輯知識庫::
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

產生答案:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
