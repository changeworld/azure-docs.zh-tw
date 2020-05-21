---
title: Markdown 格式-QnA Maker
description: 以下是您可以在 QnA Maker 的回應文字中使用的 markdown 格式清單。
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: 38532783c38263b3f1364e30125414958eab1394
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652763"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>QnA Maker 回應文字中支援的 Markdown 格式

QnA Maker 會將回應文字儲存為 markdown。 Markdown 有許多種。 為了確保正確地傳回並顯示回應文字，請使用此參考。

請使用 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** \(英文\) 教學課程來驗證您的 Markdown。 此教學課程具有 [Try it] \(試用\)**** 功能，可快速進行複製/貼上驗證。

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>何時使用 rich text 編輯與 markdown

使用者可以使用 [格式化] 工具列來快速選取和格式化文字，以提供豐富的答案[文字編輯](How-To/edit-knowledge-base.md#add-an-editorial-qna-set)。

當您需要自動產生內容來建立要匯入做為 CI/CD 管線一部分或用於[批次測試](Quickstarts/batch-testing.md)的知識庫時，Markdown 是較好的工具。

## <a name="supported-markdown-format"></a>支援的 markdown 格式

以下是您可以在 QnA Maker 的回應文字中使用的 markdown 格式清單。

|目的|格式|範例 Markdown|轉譯<br>如聊天機器人中所顯示|
|--|--|--|--|
2個句子之間的新行。|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![將兩個句子之間的新行格式化](./media/qnamaker-concepts-datasources/format-newline.png)|
|從 h1 到 h6 的標頭，的數位 `#` 代表哪一個標頭。 1 `#` 是 h1。|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![具有 markdown 標頭的格式](./media/qnamaker-concepts-datasources/format-headers.png)<br>![格式為 markdown 標頭 H1 至 H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|斜體 |`*text*`|`How do I create a bot with *QnA Maker*?`|![具有斜體的格式](./media/qnamaker-concepts-datasources/format-italics.png)|
|強式（粗體）|`**text**`|`How do I create a bot with **QnA Maker**?`|![具有強式標記的格式（粗體）](./media/qnamaker-concepts-datasources/format-strong.png)|
|連結的 URL|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL 的格式（超連結）](./media/qnamaker-concepts-datasources/format-url.png)|
|* 公用影像的 URL|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![公用影像 URL 的格式 ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|刪除線|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![刪除線的格式](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|粗體和斜體|`***text***`|`How can I create a ***QnA Maker*** bot?`|![粗體和斜體的格式](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|連結的粗體 URL|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![粗體 URL 的格式](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|連結的斜體 URL|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![斜體 URL 的格式](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape markdown 符號|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![斜體 URL 的格式](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|已排序清單|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>上述範例使用內建于 markdown 的自動編號。<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>上述範例使用明確編號。|![已排序清單的格式](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|未排序清單|`\n * item1 \n * item2`<br>或<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![未排序清單的格式](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|巢狀清單|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>您可以將已排序和未排序的清單一併嵌套在一起。 索引標籤 `\t` 表示子項目的縮排層級。|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![嵌套未排序清單的格式](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![嵌套排序清單的格式](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

* QnA Maker 不會以任何方式處理影像。 這是用戶端應用程式用來呈現影像的角色。

如果您想要使用更新/取代知識庫 Api 來新增內容，且內容/檔案包含 html 標籤，您可以藉由確保標記的開頭和結尾是以編碼格式來轉換，藉以保留檔案中的 HTML。

| 保留 HTML  | API 要求中的標記法  | 標記法（KB） |
|-----------|---------|-------------------------|
| 是 | \&lt; br \& gt; | &lt;b&gt; |
| 是 | \&lt; h3 \& gt; 標頭 \& lt;/h3 \& gt; | &lt;h3 &gt; 標頭 &lt; /h3&gt; |

此外，在 KB 中，CR LF （\r\n）會轉換為 \n。 LF （\n）會保持不被保留。 如果您想要將任何 escape 序列（例如 \t 或 \n）轉義，您可以使用反斜線，例如： ' \\ \\ r \\ \\ n ' 和 ' \\ \\ t '

## <a name="next-steps"></a>後續步驟

請參閱批次測試[檔案格式](reference-tsv-format-batch-testing.md)。
