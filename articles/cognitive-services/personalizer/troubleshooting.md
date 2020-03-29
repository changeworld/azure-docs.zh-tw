---
title: 故障排除 - 個人化程式
description: 本文包含有關個人化器的常見問題解答解答。
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336021"
---
# <a name="personalizer-troubleshooting"></a>個人化故障排除

本文包含有關個人化器的常見問題解答解答。

## <a name="transaction-errors"></a>事務錯誤

<details>
<summary><b>我從服務收到 HTTP 429（請求太多）回應。我能做什麼？</b></summary>

**答**：如果您在創建個人化實例時選擇了免費價格層，則允許的 Rank 請求數存在配額限制。 查看 Rank API 的 API 呼叫速率（在個人化工具資源的 Azure 門戶中的"指標"窗格中），並調整定價層（在定價層窗格中），如果呼叫量預計增長超過所選定價層的閾值。

</details>

<details>
<summary><b>我在排名或獎勵 API 上收到 5xx 錯誤。我該怎麼辦？</b></summary>

**答**：這些問題應該是透明的。 如果它們繼續，請在"**支援 + 故障排除**"部分中為個人化工具資源選擇"支援 + 故障排除"部分中**的新支援請求**來聯繫支援人員。

</details>

## <a name="learning-loop"></a>學習迴圈

<details>
<summary>
<b>如果沒有個人化程式，學習迴圈不會達到與系統100%匹配。如何解決此問題？</b></summary>

**答**：你通過學習迴圈沒有達到目標的原因：
* 使用 Rank API 呼叫發送的功能不足
* 發送的功能中的 Bug - 例如將非聚合要素資料（如時間戳記）發送到 Rank API
* 具有迴圈處理的 Bug - 例如不向獎勵 API 發送獎勵資料的事件

要進行修復，您需要通過更改發送到迴圈的功能來更改處理，或者確保獎勵是正確評估 Rank 回應的品質。

</details>

<details>
<summary>
<b>學習迴圈似乎沒有學習。如何解決此問題？</b></summary>

**答**：在排名呼叫有效確定優先順序之前，學習迴圈需要幾千個獎勵呼叫。

如果您不確定學習迴圈當前的行為情況，請運行[離線評估](concepts-offline-evaluation.md)，並應用已更正的學習策略。

</details>

<details>
<summary><b>我不斷獲得所有專案相同的概率的排名結果。我如何知道個人化者正在學習？</b></summary>

**答**：當排名 API 結果剛剛啟動且具有_空_模型時，或者當您重置個人化器迴圈時，您的模型仍在**模型更新頻率**期間內，則個人化程式在 Rank API 結果中返回相同的概率。

當新的更新期間開始時，將使用更新的模型，您將看到概率更改。

</details>

<details>
<summary><b>學習迴圈是學習，但似乎不再學習，排名結果的品質不是那麼好。我該怎麼辦？</b></summary>

**答案**：
* 請確保在 Azure 門戶中完成並應用了該個人化工具資源（學習迴圈）的一個評估。
* 確保所有獎勵都通過獎勵 API 發送並處理。

</details>


<details>
<summary><b>我如何知道學習迴圈正在定期更新，並用於對資料進行評分？</b></summary>

**答**：您可以在 Azure 門戶的 **"模型和學習設置"** 頁中找到模型上次更新的時間。 如果您看到舊的時間戳記，則很可能是因為您沒有發送"排名"和"獎勵"呼叫。 如果服務沒有傳入資料，則不會更新學習。 如果您看到學習迴圈更新不夠頻繁，則可以編輯迴圈的**模型更新頻率**。

</details>

## <a name="offline-evaluations"></a>離線評估

<details>
<summary><b>離線評估的功能重要性返回包含數百或數千個專案的長清單。發生了什麼事？</b></summary>

**答**：這通常是由於時間戳記、使用者 ID 或其他一些細細微性功能發送。

</details>

<details>
<summary><b>我創建了一個離線評估，它幾乎立即成功。為什麼？我沒有看到任何結果？</b></summary>

**答**：離線評估使用該時間段內事件訓練的模型資料。 如果在評估開始和結束時間之間的時間段內未發送任何資料，則資料將完成，而不會產生任何結果。 通過選擇已發送到個人化器的事件的時間範圍，提交新的離線評估。

</details>


## <a name="learning-policy"></a>學習政策

<details>
<summary><b>如何導入學習策略？</b></summary>

**答**：詳細瞭解[學習策略概念](concept-active-learning.md#understand-learning-policy-settings)[以及如何應用](how-to-manage-model.md)新的學習策略。 如果不想選擇學習策略，可以使用[離線評估](how-to-offline-evaluation.md)根據當前事件建議學習策略。

</details>

## <a name="security"></a>安全性

<details>
<summary><b>我的迴圈的 API 金鑰已洩露。我能做什麼？</b></summary>

**答**：您可以在將用戶端交換為使用另一個金鑰後重新生成一個金鑰。 具有兩個鍵允許您以延遲的方式傳播金鑰，而無需有任何停機時間。 作為安全措施，我們建議在常規週期中執行此操作。

</details>

## <a name="next-steps"></a>後續步驟

[配置模型更新頻率](how-to-settings.md#model-update-frequency)