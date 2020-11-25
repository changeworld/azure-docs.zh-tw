---
title: 標記實體範例語句
description: 瞭解如何在 LUIS 入口網站的 [意圖詳細資料] 頁面中，使用子實體在範例語句中標記機器學習實體。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019730"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>在範例語句中標示機器學習實體

在範例語句中標記實體，會提供 LUIS 一個實體內容的範例，以及實體可在語句中出現的位置。

您可以為機器學習的實體和子實體加上標籤。

因為您無法標記正則運算式、清單或預先建立的實體，所以請建立實體或列，然後將這些實體新增為實體或列的功能（如果適用）。

## <a name="label-example-utterances-from-the-intent-detail-page"></a>從意圖詳細資料頁面語句標籤範例

若要為語句中的實體加上標籤範例，請選取語句的意圖。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 選取具有您想要加上標籤之範例語句的意圖，以使用實體進行解壓縮。
1. 選取您要加上標籤的文字，然後選取實體。

## <a name="two-techniques-to-label-entities"></a>標記實體的兩個技術

意圖詳細資料頁面支援兩種標記技術。
* 從 [實體](#label-with-the-entity-palette-visible) 選擇區中選取實體或列，然後選取 [範例語句文字]。 這是建議的方法，因為您可以根據您的架構，以視覺方式確認您正在使用正確的實體或列。
* 先選取範例語句中的文字。 當您這樣做時，就會顯示 [標記選項](#how-to-label-entity-from-in-place-menu) 的快顯功能表。

## <a name="label-with-the-entity-palette-visible"></a>顯示實體調色板的標籤

在您 [使用實體規劃您的架構](luis-how-plan-your-app.md)之後，請在標記時讓 **實體調色板** 保持可見。 **實體** 選擇區會提醒您計畫要解壓縮哪些實體。

若要存取 **實體** 選擇區，請 **@** 在範例語句清單上方的內容工具列中選取符號。

> [!div class="mx-imgBorder"]
> ![意圖詳細資料頁面上實體選擇區的螢幕擷取畫面。](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>如何從實體選擇區為實體加上標籤

實體調色盤會提供先前標記體驗的替代方案。 這可讓您對文字使用筆刷，以立即使用實體來為其加上標籤。

1. 選取語句資料表右上方的符號來開啟實體選擇區 **@** 。

2. 從您要加上標籤的調色板中選取實體。 此動作會以視覺化方式顯示新的資料指標。 當您在 LUIS 入口網站中移動時，游標會在滑鼠之後。

3. 在範例語句中，使用資料指標 _繪製_ 實體。

    > [!div class="mx-imgBorder"]
    > ![螢幕擷取畫面顯示以游標繪製的實體。](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>從實體選擇區將實體新增為功能

實體選擇區的下方區段可讓您將功能新增至目前選取的實體。 您可以從所有現有的實體和片語清單中選取，或建立新的片語清單。

> [!div class="mx-imgBorder"]
> ![實體選擇區與實體功能的螢幕擷取畫面](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>標記實體角色

實體角色會使用 **實體** 選擇區來標記。

1. 在 [意圖詳細資料] 頁面中，從操作工具列中選取 [實體選擇區]。
1. 開啟實體選擇區之後，請從實體清單中選取實體。
1. 在實體清單下方，選取現有的角色。
1. 在範例語句文字中，以實體角色標記文字。

## <a name="how-to-label-entity-from-in-place-menu"></a>如何從就地功能表標記實體

標籤可讓您快速選取語句中的文字，並為其加上標籤。 您也可以從加上標籤的文字建立機器學習實體或清單實體。

請考慮範例語句 `hi, please I want a cheese pizza in 20 minutes` 。

選取最左邊的文字，然後選取實體的最右邊文字，然後從就地功能表中，挑選您想要加上標籤的實體。

> [!div class="mx-imgBorder"]
> ![標記完整機器學習實體](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>檢閱標記文字

標記之後，請參閱範例語句，並確定選取的文字範圍已加上所選實體的底線。 實線表示文字已標記。

> [!div class="mx-imgBorder"]
> ![標記完整機器學習實體](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>確認預測實體

如果文字範圍周圍有虛線方塊，則表示文字已被預測，但尚未 _標記_。 若要將預測轉換成標籤，請選取 [語句] 資料列，然後從內容工具列中選取 [ **確認實體** ]。

## <a name="relabeling-over-existing-entities"></a>重新標記現有的實體

如果您重新標示已標記的文字，LUIS 可能會分割或合併現有的標籤。

## <a name="labeling-for-punctuation"></a>標點符號的標記

您不需要為標點符號加上標籤。 使用 [應用程式設定](luis-reference-application-settings.md) 來控制標點符號對語句預測的影響。

## <a name="unlabel-entities"></a>將實體取消標記

> [!NOTE]
> 只有機器學習的實體可以未標記。 您無法標記或解除標記正則運算式實體、清單實體或預先建立的實體。

若要解除標記實體，請選取實體，然後從就地功能表中選取 [ **解除標記** ]。

> [!div class="mx-imgBorder"]
> ![顯示 unlabeling 實體的螢幕擷取畫面](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>父項和子系實體的自動標記

如果您要標記父實體，可以根據目前定型的版本來預測的任何列都會加上標籤。

如果您要標記列，父系將會自動標記。

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>非機器學習實體的自動標記

非機器學習實體包括預建的實體、正則運算式實體、清單實體和模式。任何實體。 這些會由 LUIS 自動進行標記，因此使用者不需要手動標記。

## <a name="intent-prediction-errors"></a>意圖預測錯誤

意圖預測錯誤表示系統不會預測意圖的範例語句（假設目前已定型的應用程式）。

瞭解如何在意圖詳細資料頁面上 [查看這些錯誤](luis-how-to-add-intents.md#intent-prediction-errors) 。

## <a name="entity-prediction-errors"></a>實體預測錯誤

實體預測錯誤表示預測的實體不符合標記的實體。 這會以語句旁邊的警告指標加以視覺化。

> [!div class="mx-imgBorder"]
> ![機器學習實體的實體選擇區](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>後續步驟

使用[儀表板](luis-how-to-use-dashboard.md)並且[檢閱端點語句](luis-how-to-review-endpoint-utterances.md)，來改善應用程式的預測品質。
