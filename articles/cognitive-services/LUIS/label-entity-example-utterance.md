---
title: 標記實體範例語句
description: 瞭解如何在 LUIS 入口網站的意圖詳細資料頁面中的範例語句中，以子實體標示機器學習實體。
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 959b9c6b25a7a76a87112fcbd1a10e7da12db1dd
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722638"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>在範例語句中標示機器學習服務實體

在範例語句中標記實體，會提供 LUIS 一個實體內容的範例，以及實體可在語句中出現的位置。

您可以為機器學習的實體和子實體加上標籤。

因為您無法將正則運算式、清單或預先建立的實體加上標籤，所以請建立一個實體或列，然後將這些實體加入至實體或列的功能（如果適用的話）。

## <a name="label-example-utterances-from-the-intent-detail-page"></a>從意圖詳細資料頁面語句標籤範例

若要在語句內標記實體的範例，請選取語句的意圖。

1. 登入[LUIS 入口網站](https://www.luis.ai)，並選取您的**訂**用帳戶和**撰寫資源**，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。
1. 選取具有您想要加上標籤以供與實體進行提取之範例語句的意圖。
1. 選取您要加上標籤的文字，然後選取 [實體]。

## <a name="two-techniques-to-label-entities"></a>為實體加上標籤的兩種技術

意圖詳細資料頁面支援兩種標籤技術。
* 從 [[實體] 調色板](#label-with-the-entity-palette-visible)選取 [實體] 或 [列]，然後選取 [範例語句文字]。 這是建議的方法，因為您可以根據您的架構，以視覺方式驗證您是否使用正確的實體或列。
* 先在範例語句文字中選取。 當您這麼做時，會顯示卷[標選項](#how-to-label-entity-from-in-place-menu)的快顯功能表。

## <a name="label-with-the-entity-palette-visible"></a>顯示實體調色板的標籤

在您[規劃架構與實體](luis-how-plan-your-app.md)之後，請在標記時讓**實體調色板**保持可見。 [**實體] 調色板**會提醒您計畫要解壓縮的實體。

若要存取**Entity 調色板**，請在 **@** [範例語句] 清單上方的內容相關工具列中選取符號。

> [!div class="mx-imgBorder"]
> ![意圖詳細資料頁面上實體調色板的螢幕擷取畫面。](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>如何從 Entity 調色板標記實體

實體調色盤會提供先前標記體驗的替代方案。 這可讓您對文字使用筆刷，以立即使用實體來為其加上標籤。

1. 選取語句資料表右上方的符號，以開啟 [實體] 調色板 **@** 。

2. 從您要加上標籤的調色板中選取實體。 此動作會以視覺化方式顯示新的資料指標。 當您在 LUIS 入口網站中移動時，游標會跟隨滑鼠。

3. 在範例語句中，使用資料指標_繪製_實體。

    > [!div class="mx-imgBorder"]
    > ![機器學習實體的實體調色板](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>從 Entity 調色板新增實體做為功能

實體選擇區的下半部可讓您將功能新增至目前選取的實體。 您可以從所有現有的實體和片語清單中選取，或建立新的片語清單。

> [!div class="mx-imgBorder"]
> ![實體調色板的螢幕擷取畫面，實體為功能](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>標記實體角色

實體角色會使用**實體**選擇區來標示。

1. 在 [意圖詳細資料] 頁面中，從操作工具列中選取 [實體選擇區]****。
1. 開啟實體選擇區之後，請從實體清單中選取實體。
1. 在 [實體] 清單底下，選取現有的角色。
1. 在範例語句文字中，以實體角色標記文字。

## <a name="how-to-label-entity-from-in-place-menu"></a>如何從就地功能表標記實體

就地標記標籤可讓您快速選取語句內的文字，並為其加上標籤。 您也可以從加上標籤的文字建立機器學習服務實體或清單實體。

請考慮範例語句 `hi, please I want a cheese pizza in 20 minutes` 。

選取最左邊的文字，然後選取實體的最右邊文字，然後從 [就地] 功能表中，挑選您想要加上標籤的實體。

> [!div class="mx-imgBorder"]
> ![標籤完成機器學習實體](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>檢閱標記文字

標記之後，請參閱範例語句，並確定選取的文字範圍已加上所選實體的底線。 實線表示文字已標記。

> [!div class="mx-imgBorder"]
> ![標示完整的機器學習實體](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>確認預測實體

如果文字範圍周圍有虛線方塊，則表示文字已預測但尚未_標示為標記_。 若要將預測轉換成標籤，請選取 [語句] 資料列，然後從內容相關的工具列中選取 [**確認實體**]。

## <a name="relabeling-over-existing-entities"></a>重新標記現有的實體

如果您重新標示已標記的文字，LUIS 可能會分割或合併現有標籤。

## <a name="labeling-for-punctuation"></a>標點符號的標籤

您不需要為標點符號加上標籤。 使用[應用程式設定](luis-reference-application-settings.md)來控制標點符號如何影響語句預測。

## <a name="unlabel-entities"></a>將實體取消標記

> [!NOTE]
> 只有機器學習的實體可以無標記。 您無法標記或 unlabel 正則運算式實體、列出實體，或預先建立的實體。

若要 unlabel 實體，請選取實體，然後從 [就地] 功能表中選取 [ **unlabel** ]。

> [!div class="mx-imgBorder"]
> ![顯示 unlabeling 實體的螢幕擷取畫面](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>父和子實體的自動標籤

如果您要標記父實體，可以根據目前定型的版本來預測的任何列都會加上標籤。

如果您要為列加上標籤，父系會自動標示為。

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>針對非機器學習的實體自動加上標籤

非機器學習的實體包括預先建立的實體、正則運算式實體、清單實體，以及模式。任何實體。 這些會由 LUIS 自動進行標記，因此使用者不需要手動標記。

## <a name="intent-prediction-errors"></a>意圖預測錯誤

意圖預測錯誤表示在指定目前已定型應用程式的情況下，不會針對意圖預測範例語句。

瞭解如何在意圖詳細資料頁面上[查看這些錯誤](luis-how-to-add-intents.md#intent-prediction-errors)。

## <a name="entity-prediction-errors"></a>實體預測錯誤

實體預測錯誤表示預測的實體不符合標記的實體。 這會以語句旁邊的警告指標加以視覺化。

> [!div class="mx-imgBorder"]
> ![機器學習實體的實體調色板](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>後續步驟

使用[儀表板](luis-how-to-use-dashboard.md)並且[檢閱端點語句](luis-how-to-review-endpoint-utterances.md)，來改善應用程式的預測品質。