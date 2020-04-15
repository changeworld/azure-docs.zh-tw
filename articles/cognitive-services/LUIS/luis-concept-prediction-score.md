---
title: 預測分數 - LUIS
description: 預測分數指示 LUIS API 服務根據使用者陳述對預測結果的信心度。
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 709a34f0a278d8a17267c7544583798d54167dad
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382368"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>預測分數表示意圖和實體的預測準確性

預測分數指示 LUIS 對使用者話語的預測結果的信心度。

預測分數介於零 (0) 到一 (1) 之間。 高信賴度 LUIS 分數的範例是 0.99。 低信賴度的範例是 0.01。

|分數值|信賴度|
|--|--|
|1|明確相符|
|0.99|高信賴度|
|0.01|低信賴度|
|0|明確不相符|

## <a name="top-scoring-intent"></a>最高分的意圖

每個語句預測都會傳回一個最高分的意圖。 此預測是預測分數的數值比較。

## <a name="proximity-of-scores-to-each-other"></a>分數彼此的接近

前2名的分數之間可以有非常小的差異。 LUIS 除了返回最高分之外,並不表示這種接近。

## <a name="return-prediction-score-for-all-intents"></a>傳回所有意圖的預測分數

測試或端點結果可以包含所有意圖。 此配置使用正確的查詢字串名稱/值對在終結點上設置。

|預測 API|查詢字串名稱|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>檢閱具有相似分數的意圖

查看所有意圖的分數是驗證不僅確定了正確意圖,而且下一個標識的意圖分數顯著且始終低於陳述的好方法。

如果有多個意圖的預測分數相近，則 LUIS 可能會根據語句的內容，在意圖之間做切換。 要解決此問題,請繼續向具有更廣泛上下文差異的每個意圖添加陳述,或者您可以讓用戶端應用程式(如聊天機器人)對如何處理兩個頂級意圖做出程式設計選擇。

這兩個意圖,得分太緊密,可能會因**非確定性訓練**而顛倒。 第一高分可能會變成第二高分，而第二高分可能會變成第一高分。 為了防止這種情況,通過單詞選擇和區分兩個意圖的上下文,向該陳述的前兩個意圖中添加示例陳述。 兩個意圖應具有相同數目的範例語句。 若要避免因為訓練而發生反轉，區隔語句的經驗法則是分數上要有 15% 的差異。

您可以透過[使用所有資料進行訓練](luis-how-to-train.md#train-with-all-data)來關閉**非確定性訓練**。

## <a name="differences-with-predictions-between-different-training-sessions"></a>不同訓練課程之間的預測差異

當您在不同的應用中訓練同一模型時,分數不同,這種差異是因為存在**非確定性訓練**(隨機性元素)。 其次，當語句與多個意圖有任何重疊時，即意謂著相同語句的最高分意圖會根據定型變更。

如果您的聊天機器人需要特定的 LUIS 分數來表示對意圖的信心,則應使用前兩個意圖之間的分數差異。 這種情況為培訓的變化提供了靈活性。

您可以透過[使用所有資料進行訓練](luis-how-to-train.md#train-with-all-data)來關閉**非確定性訓練**。

## <a name="e-exponent-notation"></a>E (指數) 標記法

預測分數可以使用指數標記法，「顯示成」__ 高於 0-1 的範圍，例如 `9.910309E-07`。 此分數表示一個非常**小**的數字。

|E 標記法分數 |實際分數|
|--|--|
|9.910309E-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>應用程式設定

使用[應用程式設定](luis-reference-application-settings.md)控制音調符號和標點符號如何影響預測分數。

## <a name="next-steps"></a>後續步驟

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
