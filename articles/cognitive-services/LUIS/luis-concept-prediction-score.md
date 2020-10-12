---
title: 預測分數-LUIS
description: 預測分數表示 LUIS API 服務對預測結果的信賴程度（以使用者語句為基礎）。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d836273e61752ff208133466016ce7c6ff9c28fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316455"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>預測分數表示意圖和實體的預測準確性

預測分數表示 LUIS 對使用者語句預測結果的信賴程度。

預測分數介於零 (0) 到一 (1) 之間。 高信賴度 LUIS 分數的範例是 0.99。 低信賴度的範例是 0.01。

|分數值|信賴度|
|--|--|
|1|明確相符|
|0.99|高信賴度|
|0.01|低信賴度|
|0|明確不相符|

## <a name="top-scoring-intent"></a>最高分的意圖

每個語句預測都會傳回一個最高分的意圖。 此預測是預測分數的數值比較。

## <a name="proximity-of-scores-to-each-other"></a>分數彼此相近

前2個分數在兩者之間可能有極小的差異。 LUIS 不會指出傳回最高分的這個鄰近程度。

## <a name="return-prediction-score-for-all-intents"></a>傳回所有意圖的預測分數

測試或端點結果可以包含所有意圖。 您可以使用正確的查詢字串名稱/值組，在端點上設定此設定。

|預測 API|查詢字串名稱|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>檢閱具有相似分數的意圖

檢查所有意圖的分數，是一種很好的方法，可以確認唯一識別的意圖是否正確，但下一個識別意圖的分數在語句中會大幅且一致地降低。

如果有多個意圖的預測分數相近，則 LUIS 可能會根據語句的內容，在意圖之間做切換。 若要修正這種情況，請繼續將語句新增至每個意圖，並提供更多的內容差異，或您可以讓用戶端應用程式（例如聊天機器人）進行程式設計選擇，以瞭解如何處理2個最上層意圖。

這 2 個分數過於接近的意圖可能會因為**非決定性訓練**而反轉。 第一高分可能會變成第二高分，而第二高分可能會變成第一高分。 為了避免發生這種情況，請將範例語句新增至該語句的每個前兩個意圖，並以單字選擇和區分這兩個意圖的內容來進行。 兩個意圖應具有相同數目的範例語句。 若要避免因為訓練而發生反轉，區隔語句的經驗法則是分數上要有 15% 的差異。

您可以[使用所有資料來定型，以](luis-how-to-train.md#train-with-all-data)關閉**不具決定性的定型**。

## <a name="differences-with-predictions-between-different-training-sessions"></a>不同定型會話之間的預測差異

當您在不同的應用程式中定型相同的模型，而且分數不同時，這項差異是因為隨機性) 的元素 (有不具 **決定性的定型** 。 其次，當語句與多個意圖有任何重疊時，即意謂著相同語句的最高分意圖會根據定型變更。

如果聊天機器人需要特定的 LUIS 分數來指出意圖的信賴度，您應該使用前兩個意圖之間的分數差異。 這種情況為定型中的變化提供彈性。

您可以[使用所有資料來定型，以](luis-how-to-train.md#train-with-all-data)關閉**不具決定性的定型**。

## <a name="e-exponent-notation"></a>E (指數) 標記法

預測分數可以使用指數標記法，「顯示成」__ 高於 0-1 的範圍，例如 `9.910309E-07`。 此分數表示一個非常**小**的數字。

|E 標記法分數 |實際分數|
|--|--|
|9.910309E-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>應用程式設定

使用 [應用程式設定](luis-reference-application-settings.md) 來控制如何對預測分數造成變音符號和標點符號的影響。

## <a name="next-steps"></a>後續步驟

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
