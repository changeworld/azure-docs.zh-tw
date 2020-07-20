---
title: 疑難排解-個人化工具
description: 本文包含有關個人化工具常見問題的解答。
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: ca19fbfc505e3e46338a0930773b1879dce788c1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586247"
---
# <a name="personalizer-troubleshooting"></a>個人化工具疑難排解

本文包含有關個人化工具常見問題的解答。

## <a name="configuration-issues"></a>設定問題

### <a name="i-changed-a-configuration-setting-and-now-my-loop-isnt-performing-at-the-same-learning-level-what-happened"></a>我變更了一種設定，現在我的迴圈不會在相同的學習層級執行。 發生什麼事？

某些設定值會[重設您的模型](how-to-settings.md#settings-that-include-resetting-the-model)。 應謹慎規劃設定變更。

### <a name="when-configuring-personalizer-with-the-api-i-received-an-error-what-happened"></a>使用 API 設定個人化工具時，我收到錯誤。 發生什麼事？

如果您使用單一 API 要求來設定您的服務，並變更您的學習行為，將會收到錯誤。 您需要建立兩個不同的 API 呼叫：首先，設定您的服務，然後切換學習行為。

## <a name="transaction-errors"></a>交易錯誤

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>我收到來自服務的 HTTP 429 （太多要求）回應。 我該怎麼處理？

如果您在建立個人化工具實例時選擇了免費的定價層，則允許的排名要求數目會有配額限制。 檢查您的排名 API 的 API 呼叫率（在個人化工具資源 Azure 入口網站的 [計量] 窗格中），並調整定價層（在 [定價層] 窗格中），如果您的呼叫量預期會增加到所選定價層的閾值以外。

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>我在排名或獎勵 Api 上遇到5xx 錯誤。 我該怎麼辦？

這些問題應該是透明的。 如果他們繼續，請在個人化工具資源的 Azure 入口網站中，選取 [**支援 + 疑難排解**] 區段中的 [**新增支援要求**]，以連線支援。

## <a name="learning-loop"></a>學習迴圈

### <a name="the-learning-loop-doesnt-attain-a-100-match-to-the-system-without-personalizer-how-do-i-fix-this"></a>學習迴圈不會在沒有個人化工具的情況下，達到與系統之間的100% 比對。 我要如何修正此問題？

您不能透過學習迴圈達成目標的理由：
* 使用排名 API 呼叫傳送的功能不足
* 已傳送的功能中的 bug-例如將非匯總功能資料（例如時間戳記）傳送到排名 API
* 具有迴圈處理的 bug-例如，未傳送報酬資料給事件的獎勵 API

若要修正此問題，您必須變更傳送到迴圈的功能，或確定報酬是對排名回應品質的正確評估。

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>學習迴圈似乎沒有學習。 我要如何修正此問題？

學習迴圈需要數千個報酬電話，順位呼叫才會有效率地設定優先順序。

如果您不確定學習迴圈目前的行為，請執行[離線評估](concepts-offline-evaluation.md)，並套用已更正的學習原則。

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>我持續取得所有專案的排名結果與所有相同的機率。 如何? 知道個人化工具是學習嗎？

當個人化工具剛啟動且具有_空白_模型，或當您重設個人化工具迴圈，而且您的模型仍在**模型更新頻率**期間內時，會在排名 API 結果中傳回相同的機率。

當新的更新期間開始時，會使用更新的模型，而您會看到機率變更。

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>學習迴圈已經學習，但似乎無法再學習，而且排名結果的品質並不好。 我該怎麼辦？

* 請確定您已完成，並在該個人化工具資源（學習迴圈）的 Azure 入口網站中套用一個評估。
* 請確定所有報酬都已透過獎勵 API 傳送，並已處理。

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>如何? 知道學習迴圈會定期更新，並用來對我的資料進行評分嗎？

您可以在 Azure 入口網站的 [**模型與學習設定**] 頁面中，找到模型上次更新的時間。 如果您看到舊的時間戳記，很可能是因為您未傳送排名和報酬電話。 如果服務沒有傳入的資料，就不會更新學習。 如果您看到學習迴圈的更新頻率不足，您可以編輯迴圈的**模型更新頻率**。

## <a name="offline-evaluations"></a>離線評估

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>離線評估的功能重要性會傳回包含數百或數千個專案的長清單。 發生什麼事？

這通常是因為時間戳記、使用者識別碼或一些其他細微的功能在中傳送。

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>我建立了離線評估，而且幾乎會立即成功。 這是為什麼？ 我看不到任何結果嗎？

離線評估會在該時段內使用來自事件的定型模型資料。 如果您未在評估開始和結束時間的期間內傳送任何資料，則會在沒有任何結果的情況下完成。 藉由選取您知道已傳送至個人化工具之事件的時間範圍，以提交新的離線評估。

## <a name="learning-policy"></a>學習原則

### <a name="how-do-i-import-a-learning-policy"></a>如何? 匯入學習原則嗎？

深入瞭解[學習原則概念](concept-active-learning.md#understand-learning-policy-settings)，以及[如何](how-to-manage-model.md)套用新的學習原則。 如果您不想要選取學習原則，可以使用[離線評估](how-to-offline-evaluation.md)，根據您目前的事件來建議學習原則。


## <a name="security"></a>安全性

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>我的迴圈的 API 金鑰已遭入侵。 我該怎麼處理？

您可以在交換用戶端之後重新產生一個金鑰，以使用其他金鑰。 有兩個金鑰可讓您以延遲方式傳播金鑰，而不需要停機。 我們建議您在定期執行此動作，做為安全性措施。


## <a name="next-steps"></a>後續步驟

[設定模型更新頻率](how-to-settings.md#model-update-frequency)