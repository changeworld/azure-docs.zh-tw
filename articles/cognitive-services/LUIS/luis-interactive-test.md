---
title: 在 LUIS 入口網站中測試應用程式
description: 使用 Language Understanding (LUIS) 來持續調整您的應用程式，以改善應用程式及提升其語言理解能力。
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 574bacdb5e1f167c9c9174d4a119552391059004
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677726"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>在 LUIS 入口網站中測試您的 LUIS 應用程式

[測試](luis-concept-test.md)應用程式是一種反覆程序。 在您定型 LUIS 應用程式之後，請使用範例語句來測試它，查看它是否能正確地辨識意圖和實體。 若為否，請更新 LUIS 應用程式、訓練，並重新測試。

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>測試前訓練

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。
1. 若要針對最新版本的作用中應用程式進行測試，請從頂端功能表中選取 [**訓練**]，然後再進行測試。

## <a name="test-an-utterance"></a>測試語句

測試語句不應該與應用程式中的任何範例語句完全相同。 測試語句應該包含您預期使用者的文字選擇、片語長度和實體使用方式。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。

1. 若要存取 [**測試**] 滑出面板，請選取應用程式上方面板中的 [**測試**]。

    > [!div class="mx-imgBorder"]
    > ![訓練 & 測試應用程式頁面](./media/luis-how-to-interactive-test/test.png)

1. 在文字方塊中輸入語句，並選取 Enter。 您可以針對 [測試]**** 輸入任意數目的測試語句，但一次只能輸入一個語句。

1. 該語句，連同評分最高的意圖及其分數，會被新增至文字方塊底下的語句清單中。

    > [!div class="mx-imgBorder"]
    > ![互動式測試識別出錯誤意圖](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>檢查預測

您會在 [**檢查**] 面板中檢查測試結果的詳細資料。

1. [測試]**** 滑出面板開啟時，請針對您想要比較的語句選取 [檢查]****。

    > [!div class="mx-imgBorder"]
    > ![選取 [檢查] 按鈕以查看測試結果的相關詳細資料](./media/luis-how-to-interactive-test/inspect.png)

1. [**檢查**] 面板隨即出現。 該面板包含評分最高的意圖，以及任何已識別的實體。 此面板會顯示所選語句的預測。

    > [!div class="mx-imgBorder"]
    > ![測試檢查面板的部分螢幕擷取畫面](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>新增至範例語句

從 [檢查] 面板中，您可以選取 [**新增至範例語句**]，將測試語句新增至意圖。

## <a name="disable-required-features"></a>停用必要功能

此切換可協助您根據所需的功能，判斷定型的應用程式是否能正確預測您的實體。 預設設定是在預測期間視需要套用此功能。 如果不需要列的功能，請選取此切換來查看預測的結果。

### <a name="when-to-disable-required-features"></a>停用必要功能的時機

定型的應用程式可能會根據下列其中一項來錯估機器學習的實體：
* 範例語句的標記不正確。
* 所需的功能不符合文字。

範例是機器學習的實體，具有人員名稱的列。

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="LUIS 入口網站機器學習的實體架構與必要功能的螢幕擷取畫面":::

此機器學習的實體的範例語句為： `Assign Bob Jones to work on the new security feature` 。

此提取應 `security feature` 作為票證描述，並 `Bob Jones` 作為工程師，這是實體的兩個子實體 `Assign ticket` 。

為了協助列成功預測，請將預先建立的實體[PersonName](luis-reference-prebuilt-person.md) aa a 功能新增至 `engineer` 列。 如果您將此功能設為必要，這表示只有在針對文字預測 PersonName 預建實體時，才會解壓縮列。 這表示文字中不會使用 PersonName 列預測的任何名稱，將不會當做標記的列傳回 `engineer` 。

當您使用 [互動式測試] 窗格，並看到具有必要功能的列時，不會進行預測，請切換此設定，以查看列是否會在沒有必要功能的情況下預測。 列可能會因為正確標記的範例語句，而不需要此功能就能正確預測。

## <a name="view-sentiment-results"></a>檢視情感結果

若已在 [[發行](luis-how-to-publish-app.md#enable-sentiment-analysis)]**** 頁面上設定 [情感分析]****，測試結果將會包含在語句中找到的情感。

## <a name="correct-matched-patterns-intent"></a>修正相符模式的意圖

若您有使用[模式](luis-concept-patterns.md)且語句有符合某個模式，但系統所預測的意圖是錯誤的，請選取模式旁邊的 [編輯]**** 連結，然後選取正確的意圖。

## <a name="compare-with-published-version"></a>與已發行的版本比較

您可以搭配已發行的[端點](luis-glossary.md#endpoint)版本來測試應用程式的作用中版本。 在 [檢查]**** 面板中，選取 [與已發行比較]****。 針對已發行模型所做的任何測試，將會從您的 Azure 訂用帳戶配額餘額中扣除。

> [!div class="mx-imgBorder"]
> ![[與已發行比較]](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>在 [測試] 面板中檢視端點 JSON
您可以選取 [顯示 JSON 檢視]**** 來檢視針對比較傳回的端點 JSON。

> [!div class="mx-imgBorder"]
> ![已發行的 JSON 回應](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>[測試] 面板中的其他設定

### <a name="luis-endpoint"></a>LUIS 端點

若您有數個 LUIS 端點，請使用 [測試] 面板 [發行] 窗格上的 [其他設定]**** 連結，以變更用於測試的端點。 若您不確定該使用哪一個端點，請選取預設的 [Starter_Key]****。

> [!div class="mx-imgBorder"]
> ![已醒目提示 [其他選項] 的 [測試] 面板](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>批次測試
請參閱批次測試的[概念](luis-concept-batch-test.md)，並了解[如何](luis-how-to-batch-test.md)測試語句批次。

## <a name="next-steps"></a>後續步驟

若測試顯示出您的 LUIS 應用程式無法識別正確的意圖和實體，則您可以透過標示更多語句或新增功能，以提升 LUIS 應用程式的精確度。

* [使用 LUIS 標示建議的語調](luis-how-to-review-endpoint-utterances.md)
* [使用功能來改善 LUIS 應用程式效能](luis-how-to-add-features.md)
