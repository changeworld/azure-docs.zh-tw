---
title: 檢視使用者話語 - LUIS
description: 使用主動式學習，您可檢閱端點語句中正確的意圖和實體。 LUIS 會選擇不確定的端點語句。
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8d267fc441dc2cbf7f8ae3746486d5e7be55f135
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546859"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>檢閱端點語句以啟用主動式學習的概念
主動式學習是改善預測精確度且最簡單實作的三種策略之一。 使用主動式學習，您可檢閱端點語句中正確的意圖和實體。 LUIS 會選擇不確定的端點語句。

## <a name="what-is-active-learning"></a>何謂主動式學習
主動式學習是一個兩步驟程序。 首先，LUIS 會選取它在需要驗證的應用程式端點收到的語句。 第二個步驟是由應用程式擁有者或共同作業者來驗證可供[檢閱](luis-how-to-review-endpoint-utterances.md)的所選語句，包括正確的意圖和意圖內的任何實體。 在檢閱語句之後，訓練並重新發佈應用程式。

## <a name="which-utterances-are-on-the-review-list"></a>檢閱清單上有哪個語句
當熱門引發意圖的分數很低或前兩個意圖的分數太接近時，LUIS 會將語句新增至檢閱清單。

## <a name="single-pool-for-utterances-per-app"></a>每個應用程式中適用於語句的單一集區
**檢閱端點語句**清單不會根據版本來變更。 不論您正在編輯的語句版本為何，也不論在端點上發佈的應用程式版本為何，都只有一個要檢閱的語句集區。

在[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9)中,版本名稱是必需的,必須存在於應用程式中,但超出驗證之外不使用。 審核陳述適用於整個應用程式。 如果從一_個版本_中刪除話語,則所有版本都受到影響。

## <a name="where-are-the-utterances-from"></a>語句來自何處
終結點陳述取自對應用程式的 HTTP 終結點的最終用戶查詢。 如果您的應用程式並未發佈，或尚未接獲叫用，則表示您沒有任何要檢閱的語句。 如果未收到特定意圖或實體的端點叫用，則表示您沒有包含該意圖或實體而要檢閱的語句。

## <a name="schedule-review-periodically"></a>定期排程檢閱
檢閱建議的語句不需要每天進行，但應屬於 LUIS 定期維護的一部分。

## <a name="delete-review-items-programmatically"></a>以程式設計方式刪除檢閱項目
使用**[刪除未標記的話語 API。](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** 先**[匯出記錄檔來備份這些語句](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**，然後再進行刪除。

## <a name="enable-active-learning"></a>開啟主動學習

要啟用活動學習,必須記錄用戶查詢。 這是透過查詢字串參數與值呼叫[終結點查詢](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)來實現的`log=true`。

## <a name="next-steps"></a>後續步驟

* 如何[檢閱](luis-how-to-review-endpoint-utterances.md)端點語句
