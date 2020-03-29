---
title: 知識庫的生命週期 - QnA Maker
description: QnA Maker 在反覆的週期中學習模型變更、語句範例、發佈資料以及從端點查詢收集資料時，會有最佳學習成效。
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914947"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker 中的知識庫生命週期
QnA Maker 在反覆的週期中學習模型變更、語句範例、發佈資料以及從端點查詢收集資料時，會有最佳學習成效。

![撰寫循環](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>建立 QnA Maker 知識庫
QnA Maker 知識庫 (KB) 端點會根據知識庫的內容對使用者查詢提供最相符的解答。 創建知識庫是設置問題、答案和相關中繼資料的內容存儲庫的一次性操作。 知識庫可藉由對既有的內容編目來建立，這些內容包括常見問題集頁面、產品手冊或結構化問答組。 了解如何[建立知識庫](../quickstarts/create-publish-knowledge-base.md)。

## <a name="testing-and-updating-the-knowledge-base"></a>測試和更新知識庫

知識庫在填入內容 (透過編輯或自動擷取) 後即可供測試。 通過輸入常見使用者查詢並驗證返回的回應是否回應正確回應和足夠的置信度分數，可以通過**測試**面板在 QnA Maker 門戶中執行互動式測試。

* **要修復低置信度分數**：添加備用問題。
* **當查詢錯誤地返回[預設回應](../How-to/change-default-answer.md)** 時：向正確的問題添加新答案。

「測試-更新」的這個密封迴圈會持續執行，直到您得到滿意的結果為止。 了解如何[測試知識庫](../How-To/test-knowledge-base.md)。

對於大型 KB，請使用與[生成應答 API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api)和`isTest`正文屬性的自動測試，後者查詢`test`知識庫而不是已發佈的知識庫。

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>發佈知識庫
完成知識庫的測試後，您即可發佈知識庫。 發佈將測試知識庫的最新版本推送到表示**已發佈的**知識庫的專用 Azure 認知搜索索引。 它也會建立可在您的應用程式或聊天機器人中呼叫的端點。

如此，對測試版知識庫所做的任何變更，都不會對可能正在生產應用程式中執行的已發行版本造成影響。

這些知識庫全都可以個別進行測試。 使用 API，您可以在生成應答調用中使用`isTest`正文屬性定位知識庫的測試版本。

了解如何[發佈知識庫](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="monitor-usage"></a>監視使用量
若要能夠記錄服務的聊天記錄，您必須在[建立 QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)時啟用 Application Insights。

您可以取得服務使用情形的各種分析。 深入了解如何使用 Application Insights 取得 [QnA Maker 服務的分析](../How-To/get-analytics-knowledge-base.md)。

您可以根據從分析得出的結論，適當[更新您的知識庫](../How-To/edit-knowledge-base.md)。

## <a name="version-control-for-data-in-your-knowledge-base"></a>知識庫中資料的版本控制

資料的版本控制通過 QnA Maker 門戶中的 **"設置"** 頁上的導入/匯出功能提供。

您可以通過匯出知識庫（以或格式`.tsv``.xls`）來備份知識庫。 匯出後，將此檔作為常規原始程式碼管理檢查的一部分。

當您需要返回特定版本時，您需要從本地系統導入該檔。 匯出的知識庫**只能通過**"**設置"** 頁上的導入來使用。 不能將其用作檔或 URL 文檔資料來源。 這將用導入的檔的內容替換知識庫中當前的問題和答案。

## <a name="test-and-production-knowledge-base"></a>測試和生產知識庫
知識庫是通過 QnA Maker 創建、維護和使用的問題和答案集的存儲庫。 每個 QnA Maker 資源可以容納多個知識庫。

知識庫有兩種狀態：*測試*和*發佈*。

### <a name="test-knowledge-base"></a>測試知識庫

*測試知識庫*是當前編輯、保存和測試回應的準確性和完整性的版本。 對測試知識庫所做的更改不會影響應用程式或聊天機器人的最終使用者。 測試知識庫在 HTTP`test`請求中稱為"測試知識庫"。 QnA `test` Maker 的門戶互動式**測試**窗格提供了這些知識。

### <a name="production-knowledge-base"></a>生產知識庫

*已發佈的知識庫*是聊天機器人或應用程式中使用的版本。 發佈知識庫的操作將測試知識庫的內容置於知識庫的已發佈版本中。 由於已發佈的知識庫是應用程式通過終結點使用的版本，因此請確保內容正確且測試良好。 已發佈的知識庫在 HTTP`prod`請求中稱為"已發佈的知識庫"。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [主動學習建議](./active-learning-suggestions.md)