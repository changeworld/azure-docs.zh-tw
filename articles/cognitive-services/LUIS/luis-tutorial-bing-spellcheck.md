---
title: 更正拼錯的字組-LUIS
titleSuffix: Azure Cognitive Services
description: 將 Bing 拼字檢查 API V7 新增至 LUIS 端點查詢，可更正語句中拼錯的字組。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: f416fe8ef4f6e89d07e6065d4c9435642d9bacb9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179634"
---
# <a name="correct-misspelled-words-with-bing-search-resource"></a>使用 Bing 搜尋資源更正拼錯的字組

您可以將 LUIS 應用程式與 [Bing 搜尋](https://ms.portal.azure.com/#create/Microsoft.BingSearch) 整合，以修正語句中拼錯的字組，然後 LUIS 預測語句的分數和實體。

## <a name="create-endpoint-key"></a>建立端點金鑰

若要在 Azure 入口網站中建立 Bing 搜尋資源，請遵循下列指示：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左上角選取 [建立資源]。

3. 在搜尋方塊中，輸入 `Bing Search V7` 並選取服務。

4. 資訊面板會顯示在右邊，包含法律聲明等資訊。 選取 [建立] 開始訂用帳戶建立程序。

    :::image type="content" source="./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png" alt-text="Bing 拼寫檢查 API V7 資源":::

5. 在下一個面板中，輸入您的服務設定。 等候服務建立程序完成。

6. 建立資源之後，請移至左側的 [ **金鑰和端點** ] 分頁。 

7. 複製其中一個要加入至預測要求標頭的金鑰。 您只需要兩個金鑰的其中一個。

8. 將索引鍵加入至 `mkt-bing-spell-check-key` 預測要求標頭。

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>將金鑰新增至端點 URL
針對您想要對其套用拼寫更正的每個查詢，端點查詢需要在查詢標頭參數中傳遞的 Bing 拼字檢查資源金鑰。 您可能有可呼叫 LUIS 的聊天機器人，或者您可以直接呼叫 LUIS 端點 API。 無論呼叫端點的方式為何，每個呼叫都必須在標頭要求中包含所需的資訊，拼寫更正才能正確運作。 您必須使用 **mkt-bing-拼寫檢查** 索引鍵將值設定為索引鍵值。


## <a name="send-misspelled-utterance-to-luis"></a>將拼錯的語句傳送至 LUIS
1. 在您要傳送的預測查詢中加入拼錯的語句，例如「mountainn 的距離為何？」。 在英文中，包含一個 `n` 的 `mountain` 是正確的拼法。

2. LUIS 會以 JSON 結果回應 `How far is the mountain?`。 如果 Bing 拼字檢查 API v7 偵測到拼錯，LUIS 應用程式的 JSON 回應中的 `query` 欄位包含原始查詢，而 `alteredQuery` 欄位包含傳送至 LUIS 的更正後查詢。

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>忽略拼字錯誤

如果您不想要使用 Bing 搜尋 API v7 服務，則必須加入正確且不正確的拼寫。

有兩種解決方案：

* 標籤範例語句具有所有不同的拼寫，讓 LUIS 能夠學習正確的拼寫和打字錯誤。 相較於使用拼字檢查工具，此選項需要更多標記工作。
* 建立包含單字所有變化的片語清單。 透過此解決方案，您不需要在範例語句中標示文字變化。


> [!div class="nextstepaction"]
> [深入了解範例語句](./luis-how-to-add-entities.md)
