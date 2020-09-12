---
title: 知識庫的生命週期 - QnA Maker
description: QnA Maker 在反覆的週期中學習模型變更、語句範例、發佈資料以及從端點查詢收集資料時，會有最佳學習成效。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 7bbf729fa80e4b41a85b8dfd1080decea1bae108
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612269"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker 中的知識庫生命週期
QnA Maker 在反覆的週期中學習模型變更、語句範例、發佈資料以及從端點查詢收集資料時，會有最佳學習成效。

![撰寫循環](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>建立 QnA Maker 知識庫
QnA Maker 知識庫 (KB) 端點會根據知識庫的內容對使用者查詢提供最相符的解答。 建立知識庫是一次設定問題、解答和相關中繼資料的內容存放庫的動作。 您可以藉由編目預先存在的內容（如下來源）來建立 KB：

- 常見問題集頁面
- 產品手冊
- Q-A 組

了解如何[建立知識庫](../quickstarts/create-publish-knowledge-base.md)。

## <a name="testing-and-updating-the-knowledge-base"></a>測試和更新知識庫

知識庫在填入內容 (透過編輯或自動擷取) 後即可供測試。 您可以透過 [ **測試** ] 面板，在 QnA Maker 入口網站中完成互動式測試。 您可以輸入一般使用者查詢。 然後，您會確認傳回的回應具有正確的回應和足夠的信賴分數。


* **若要修正信賴分數下限**：新增替代問題。
* **當查詢不正確地傳回[預設回應](../How-to/change-default-answer.md)時**：請將新的答案新增至正確的問題。

「測試-更新」的這個密封迴圈會持續執行，直到您得到滿意的結果為止。 了解如何[測試知識庫](../How-To/test-knowledge-base.md)。

針對大型 Kb，使用 [GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) 和 body 屬性的自動化測試，以 `isTest` 查詢 `test` 知識庫，而不是已發行的知識庫。

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>發佈知識庫
完成知識庫的測試後，您即可發佈知識庫。 Publish 會將已測試之知識庫的最新版本推送至代表 **已發佈** 知識庫的專用 Azure 認知搜尋索引。 它也會建立可在您的應用程式或聊天機器人中呼叫的端點。

由於發行動作的緣故，對知識庫測試版本所做的任何進一步變更都會使發行的版本不受影響。 已發佈的版本可能會在生產應用程式中上線。

這些知識庫全都可以個別進行測試。 使用 Api，您可以使用 `isTest` generateAnswer 呼叫中的 body 屬性，將知識庫的測試版本設為目標。

了解如何[發佈知識庫](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="monitor-usage"></a>監視使用量
若要能夠記錄服務的聊天記錄，您必須在[建立 QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)時啟用 Application Insights。

您可以取得服務使用情形的各種分析。 深入了解如何使用 Application Insights 取得 [QnA Maker 服務的分析](../How-To/get-analytics-knowledge-base.md)。

您可以根據從分析得出的結論，適當[更新您的知識庫](../How-To/edit-knowledge-base.md)。

## <a name="version-control-for-data-in-your-knowledge-base"></a>知識庫中資料的版本控制

您可以透過 QnA Maker 入口網站中 [ **設定** ] 頁面上的 [匯入/匯出功能]，來提供資料的版本控制。

您可以使用或格式匯出知識庫來備份知識庫 `.tsv` `.xls` 。 一旦匯出之後，請將此檔案納入定期原始檔控制檢查的一部分。

當您需要回到特定版本時，您需要從本機系統匯入該檔案。 匯出的知識庫 **必須** 只能透過 [ **設定** ] 頁面上的 [匯入] 使用。 它無法當做檔案或 URL 檔資料來源使用。 這將會以匯入檔案的內容取代知識庫中目前的問題和答案。

## <a name="test-and-production-knowledge-base"></a>測試和生產知識庫
知識庫是透過 QnA Maker 建立、維護及使用的問題和解答集的儲存機制。 每個 QnA Maker 資源可以保留多個知識庫。

知識庫有兩種狀態： *測試* 和 *發佈*。

### <a name="test-knowledge-base"></a>測試知識庫

*測試知識庫*是目前編輯和儲存的版本。 測試版本已測試是否正確，以及是否有回應的完整性。 對測試知識庫所做的變更不會影響應用程式或聊天機器人的終端使用者。 測試的知識庫在 `test` HTTP 要求中稱為。 您 `test` 可以透過 QnA Maker 的入口網站互動式 **測試** 窗格來取得知識。

### <a name="production-knowledge-base"></a>生產知識庫

*已發佈的知識庫*是在聊天機器人或應用程式中使用的版本。 發佈知識庫會將其測試版本的內容放入其已發行的版本中。 已發佈的知識庫是應用程式透過端點所使用的版本。 請確定內容正確且經過妥善測試。 已發佈的知識庫 `prod` 在 HTTP 要求中稱為。


## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [主動式學習建議](./active-learning-suggestions.md)