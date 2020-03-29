---
title: 標記格式 - QnA 製造商
description: 以下是可在 QnA Maker 的答案文本中使用的標記格式清單。
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045401"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>QnA Maker 答案文本中支援的標記格式

QnA Maker 將答案文本存儲為標記。 有許多標記的味道。 為了確保正確返回和顯示答案文本，請使用此引用。

請使用 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** \(英文\) 教學課程來驗證您的 Markdown。 此教學課程具有 [Try it] \(試用\)**** 功能，可快速進行複製/貼上驗證。

## <a name="supported-markdown-format"></a>支援的降標格式

以下是可在 QnA Maker 的答案文本中使用的標記格式清單。

|目的|[格式]|範例 Markdown|轉譯<br>如聊天機器人中顯示的|
|--|--|--|--|
2個句子之間的新行。|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![在兩個句子之間設置新行格式](./media/qnamaker-concepts-datasources/format-newline.png)|
|從 h1 到 h6 的標頭`#`，表示哪個標頭的數量。 1`#`是 h1。|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![具有標記頭的格式](./media/qnamaker-concepts-datasources/format-headers.png)<br>![具有標記頭 H1 到 H5 的格式](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|斜體 |`*text*`|`How do I create a bot with *QnA Maker*?`|![格式斜體](./media/qnamaker-concepts-datasources/format-italics.png)|
|強（粗體）|`**text**`|`How do I create a bot with **QnA Maker**?`|![具有粗體強標記的格式](./media/qnamaker-concepts-datasources/format-strong.png)|
|連結的 URL|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL 格式（超連結）](./media/qnamaker-concepts-datasources/format-url.png)|
|*公共圖像的 URL|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![公共圖像 URL 的格式 ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|刪除線|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![打擊通的格式](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|粗體和斜體|`***text***`|`How can I create a ***QnA Maker*** bot?`|![粗體和斜體格式](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|連結的粗體 URL|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![粗體 URL 的格式](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|連結的斜體 URL|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![斜體 URL 的格式](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|轉義標記符號|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![斜體 URL 的格式](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|已排序清單|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>前面的示例使用內置於標記中的自動編號。<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>前面的示例使用顯式編號。|![排序清單的格式](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|未排序清單|`\n * item1 \n * item2`<br>或<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![無序清單的格式](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|巢狀清單|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>可以一起嵌套有序和無序清單。 選項卡`\t`指示子項目的縮進級別。|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![嵌套無序清單的格式](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![嵌套有序清單的格式](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA 製造商不會以任何方式處理圖像。 它是用戶端應用程式的角色來呈現映射。

如果要使用更新/替換知識庫 API 添加內容，並且內容/檔包含 html 標記，則可以通過確保以編碼格式轉換標記的打開和關閉來保留檔中的 HTML。

| 保留 HTML  | API 請求中的表示  | 以 KB 為單位的表示形式 |
|-----------|---------|-------------------------|
| 是 | \&lt;br\&gt; | &lt;Br&gt; |
| 是 | \&lt;h3\&gt;\&頭 lt;/h3\&gt; | &lt;h3&gt;&lt;頭 /h3&gt; |

此外，CR LF（\r\n）在 KB 中轉換為 \n。 LF（\n）保持不變。 如果要轉義任何逸出序列（如 \t 或 \n），則可以使用反斜線，例如\\\\："r\\\\n"\\\\和"t"

## <a name="next-steps"></a>後續步驟

查看批次處理測試[檔案格式](reference-tsv-format-batch-testing.md)。
