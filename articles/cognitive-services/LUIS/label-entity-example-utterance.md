---
title: 標記實體範例語句
description: 了解如何在 LUIS 入口網站的意圖詳細資料頁面中，以範例語句中的子元件標記機器學習實體。
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ea5fbea902c9694d9a8a6a8a5bffcf5e7234bbbd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382408"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>在範例語句中標記機器學習實體

在範例語句中標記實體，會提供 LUIS 一個實體內容的範例，以及實體可在語句中出現的位置。

## <a name="labeling-machine-learned-entity"></a>標記機器學習實體

請考量片語 `hi, please I want a cheese pizza in 20 minutes`。

1. 選取最左邊的文字，然後選取實體的最右邊文字，然後挑選您想要標記的實體，在此案例中為「完成順序」。 _完成順序_會在下圖中標示。

    > [!div class="mx-imgBorder"]
    > ![標記完整機器學習實體](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. 從快顯視窗中選取實體。 標記的完整披薩訂單實體包含標示的所有單字 (由左到右的英文)。

## <a name="review-labeled-text"></a>檢閱標記文字

標記之後，請參閱範例語句，並確定選取的文字範圍已加上所選實體的底線。 實線表示文字已標記。

> [!div class="mx-imgBorder"]
> ![標記完整機器學習實體](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>確認預測實體

如果文字範圍周圍有虛線方塊，而且實體名稱高於語句，則表示文字已進行預測，但「尚未標記」__。 若要將預測轉換為標記，請選取語句列，然後選取 [確認實體預測]****。

> [!div class="mx-imgBorder"]
> ![預測完整機器學習實體](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

或者，您可以選取文字上方的實體名稱，然後從出現的功能表中選取 [確認預測]****。

> [!div class="mx-imgBorder"]
> ![以功能表預測完整機器學習實體](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>藉由使用實體調色盤資料指標進行繪製，以標記實體

實體調色盤會提供先前標記體驗的替代方案。 這可讓您對文字使用筆刷，以立即使用實體來為其加上標籤。

1. 選取語句資料表右上方的螢光筆圖示，即可開啟實體調色盤。

    > [!div class="mx-imgBorder"]
    > ![機器學習實體的實體調色盤](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. 選取實體元件。 此動作會以視覺化方式顯示新的資料指標。 當您在入口網站中移動時，資料指標會跟隨滑鼠。

    > [!div class="mx-imgBorder"]
    > ![機器學習實體的實體調色盤](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. 在範例語句中，使用資料指標_繪製_實體。

    > [!div class="mx-imgBorder"]
    > ![機器學習實體的實體調色盤](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>標記機器學習實體的子元件

標記實體中子元件的方式與上層實體的標記方式相同。 選取文字後，快顯視窗中可用的實體會相對於出現文字的內容。 例如，如果您有 5 層級機器學習實體，而您選取的是標記為第 1 層和第 2 層的文字 (在範例語句底下由標記實體名稱表示)，則快顯視窗中可用的實體會限制為第 3 層的元件內容。 若要使用其他實體來標記文字，請選取 [標記為另一個實體]**** 選項。

> [!div class="mx-imgBorder"]
> ![機器學習實體的實體調色盤](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

只有在其父代項目也加上標籤時，才可以標記子元件。

## <a name="labeling-entity-roles"></a>標記實體角色

實體角色會使用實體選擇區來標記。

1. 在 [意圖詳細資料] 頁面中，從操作工具列中選取 [實體選擇區]****。
1. 開啟實體選擇區之後，請從實體清單中選取實體。
1. 移至 [實體偵測器]****，然後選取現有的角色或建立新的角色。
1. 在範例語句文字中，以實體角色標記文字。

## <a name="labeling-for-punctuation"></a>標點符號的標籤

您無需為標點符號添加標籤。 使用[應用程式設置](luis-reference-application-settings.md)控制標點符號是否影響陳述預測。

## <a name="unlabel-entities"></a>將實體取消標記

若要將實體取消標記，請選取文字底下的實體名稱，然後選取 [取消標記]****。 如果您嘗試取消標記的實體具有已標記的子元件，則子元件必須是先取消標記。

## <a name="editing-labels-using-the-entity-palette"></a>使用實體調色盤編輯標籤

如果您在標記時犯了錯誤，則實體調色盤就是可讓您快速編輯的簡單工具。 例如，如果實體標籤不小心跨越到額外的字組，而且其具有已標記的子元件，則您可以使用實體調色盤，在所需的較短字組範圍上使用筆刷。

例如：

1. 「披薩類型 (Pizza Type)」子元件的範圍是 "cheese pizza with"，其中包含額外的錯誤字組 -- "with"

    > [!div class="mx-imgBorder"]
    > ![機器學習實體的實體調色盤](media/label-utterances/edit-label-with-palette-1.png)

2. 使用實體調色盤來挑選「披薩類型」，然後對 "cheese pizza" 使用筆刷。 現在，其結果就是只將 "cheese pizza" 標記為「披薩類型」。

    > [!div class="mx-imgBorder"]
    > ![機器學習實體的實體調色盤](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>用於相符文字實體的標籤

相符文字實體包括預先建置的實體、規則運算式實體、清單實體和 pattern.any 實體。 這些會由 LUIS 自動進行標記，因此使用者不需要手動標記。

## <a name="entity-prediction-errors"></a>實體預測錯誤

實體預測錯誤表示預測的實體不符合標記的實體。 這會以語句旁邊的警告指標加以視覺化。

> [!div class="mx-imgBorder"]
> ![機器學習實體的實體調色盤](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>後續步驟

使用[儀表板](luis-how-to-use-dashboard.md)並且[檢閱端點語句](luis-how-to-review-endpoint-utterances.md)，來改善應用程式的預測品質。