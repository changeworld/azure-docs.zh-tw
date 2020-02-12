---
title: 在 LUIS 入口網站中測試應用程式
description: 使用 Language Understanding (LUIS) 來持續調整您的應用程式，以改善應用程式及提升其語言理解能力。
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152902"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>在 LUIS 入口網站中測試您的 LUIS 應用程式

[測試](luis-concept-test.md)應用程式是一種反復的進程。 在您定型 LUIS 應用程式之後，請使用範例語句來測試它，查看它是否能正確地辨識意圖和實體。 若為否，請更新 LUIS 應用程式、訓練，並重新測試。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>測試前訓練

若要針對最新版本的作用中應用程式進行測試，請從頂端功能表中選取 [**訓練**]，然後再進行測試。

## <a name="test-an-utterance"></a>測試語句

測試語句不應該與應用程式中的任何範例語句完全相同。 測試語句應該包含您預期使用者的文字選擇、片語長度和實體使用方式。

1. 在 [我的應用程式] 頁面上選取應用程式名稱，來存取應用程式。

1. 若要存取 [測試] 滑出面板，請選取應用程式上方面板中的 [測試]。

    > [!div class="mx-imgBorder"]
    > ![訓練 & 測試應用程式 頁面](./media/luis-how-to-interactive-test/test.png)

1. 在文字方塊中輸入語句，並選取 Enter。 您可以針對 [測試] 輸入任意數目的測試語句，但一次只能輸入一個語句。

1. 該語句，連同評分最高的意圖及其分數，會被新增至文字方塊底下的語句清單中。

    ![互動式測試識別出錯誤意圖](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>檢查分數

您可在 [檢查] 面板中檢查測試結果的詳細資料。

1. [測試] 滑出面板開啟時，請針對您想要比較的語句選取 [檢查]。

    ![選取 [檢查] 按鈕以查看測試結果的相關詳細資料](./media/luis-how-to-interactive-test/inspect.png)

1. [檢查] 面板隨即出現。 該面板包含評分最高的意圖，以及任何已識別的實體。 該面板會顯示所選語句的結果。

    ![該面板包含評分最高的意圖，以及任何已識別的實體。 該面板會顯示所選語句的結果。](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>修正評分最高的意圖

1. 若評分最高的意圖是錯誤的，請選取 [編輯] 按鈕。

1.  在下拉式清單中，為該語句選取正確的意圖。

    ![選取正確的意圖](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>檢視情感結果

若已在 [**發行**] **[ 頁面上設定 [情感分析]](luis-how-to-publish-app.md#enable-sentiment-analysis)** ，測試結果將會包含在語句中找到的情感。

![具有情感分析之 [測試] 窗格的影像](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>修正相符模式的意圖

若您有使用[模式](luis-concept-patterns.md)且語句有符合某個模式，但系統所預測的意圖是錯誤的，請選取模式旁邊的 [編輯] 連結，然後選取正確的意圖。

## <a name="compare-with-published-version"></a>與已發行的版本比較

您可以搭配已發行的[端點](luis-glossary.md#endpoint)版本來測試應用程式的作用中版本。 在 [檢查] 面板中，選取 [與已發行比較]。 針對已發行模型所做的任何測試，將會從您的 Azure 訂用帳戶配額餘額中扣除。

![[與已發行比較]](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>在 [測試] 面板中檢視端點 JSON
您可以選取 [顯示 JSON 檢視] 來檢視針對比較傳回的端點 JSON。

![已發行的 JSON 回應](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>[測試] 面板中的其他設定

### <a name="luis-endpoint"></a>LUIS 端點

若您有數個 LUIS 端點，請使用 [測試] 面板 [發行] 窗格上的 [其他設定] 連結，以變更用於測試的端點。 若您不確定該使用哪一個端點，請選取預設的 [Starter_Key]。

> [!div class="mx-imgBorder"]
> 已反白顯示其他設定連結的 ![測試面板](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>批次測試
請參閱批次測試的[概念](luis-concept-batch-test.md)，並了解[如何](luis-how-to-batch-test.md)測試語句批次。

## <a name="next-steps"></a>後續步驟

若測試顯示出您的 LUIS 應用程式無法識別正確的意圖和實體，則您可以透過標示更多語句或新增功能，以提升 LUIS 應用程式的精確度。

* [使用 LUIS 標示建議的語調](luis-how-to-review-endpoint-utterances.md)
* [使用功能來改善 LUIS 應用程式效能](luis-how-to-add-features.md)
