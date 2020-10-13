---
title: " (預覽) 的 Debug 會話概念"
titleSuffix: Azure Cognitive Search
description: 透過 Azure 入口網站來存取的偵錯工作階段，提供類似於 IDE 的環境，您可以在其中識別並修正錯誤、驗證變更，以及將變更推送至 AI 擴充管線中的技能。 偵錯工作階段處於預覽狀態。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 941d7f54bcb44653280abe264ff1cffbeae33679
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743177"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>Azure 認知搜尋中的 Debug 話

「偵錯工作階段」是一種視覺化編輯器，適用於 Azure 入口網站中的現有技能。 在偵錯工作階段中，您可以識別並解析錯誤、驗證變更，以及將變更推送至 AI 擴充管線中的生產技能。

> [!Important]
> 偵錯工作階段是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="using-debug-sessions"></a>使用偵錯工作階段

當您啟動工作階段時，服務會建立技能、索引子和索引的複本，其中會使用單一文件來測試技能。 工作階段內所做的變更會儲存到工作階段。 在偵錯工作階段內所做的變更不會影響生產技能，除非已對其認可變更。 認可變更將會覆寫生產技能。

在偵錯工作階段期間，您可以在特定文件的內容中編輯技能、檢查及驗證擴充樹狀結構中的每個節點。 一旦解決擴充管線問題後，就可以將變更儲存至工作階段，並認可生產中的技能。 

如果擴充管線沒有任何錯誤，就可以使用偵錯工作階段，以累加方式擴充文件、測試及驗證每個變更，然後再認可變更。

## <a name="creating-a-debug-session"></a>建立偵錯工作階段

若要啟動偵錯工作階段，您必須擁有現有的 AI 擴充管線，包括：資料來源、技能、索引子和索引。 若要設定偵錯工作階段，您必須為工作階段命名，並提供一般用途的儲存體帳戶，以在索引子執行期間用來快取技能執行。 您也必須選取將要執行的索引子。 索引子具有儲存至資料來源、技能和索引的參考。 偵錯工作階段會預設為資料來源中的第一份文件，或者您可以在資料來源中指定要逐步執行的文件。

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="建立偵錯工作階段":::

## <a name="debug-session-features"></a>偵錯工作階段功能

偵錯工作階段一開始會在選取的文件上執行技能。 偵錯工作階段會在技能中記錄與每個作業相關聯的其他中繼資料。 管線的技能執行所建立的中繼資料，會通知下列功能集，然後用來協助找出並修正技能的問題。

## <a name="ai-enrichments"></a>AI 擴充

隨著技能執行擴充的樹狀結構 (代表文件) 成長。 使用樹狀結構來視覺化技能或擴充的輸出，讓您可完整查看所有執行的擴充。 您可以查看整個文件，並檢查擴充樹狀結構的每個節點。 這種觀點可讓您更輕鬆地塑造物件。 這個格式也會針對樹狀結構中的每個節點，提供類型、路徑和內容的視覺提示。

## <a name="skill-graph"></a>技能圖表

[技能圖表] 視圖會提供技能的階層式視覺化表示法。 圖表是執行技能順序的由上至下表示法。 圖表下方會顯示相依於其他技能輸出的技能。 階層中相同層級的技能可以平行執行。 

選取圖表中的技能會反白顯示與其連線的技能、建立其輸入的節點，以及接受其輸出的節點。 每個技能節點都會顯示其類型、錯誤或警告，以及執行計數。 [技能圖表] 是您將選取哪些技能要用來偵錯或加強的位置。 當您選取技能時，其詳細資料就會顯示在圖表右側的 [技能詳細資料] 窗格中。

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="建立偵錯工作階段":::

## <a name="skill-details"></a>技能詳細資料

[技能詳細資料] 窗格會在 [技能圖表] 反白顯示特定技能時，顯示一組用來處理該技能的區域。 您可以檢閱和編輯技能設定的詳細資料。 已提供技能的 JSON 定義。 同時也會顯示技能執行的詳細資料，以及錯誤和警告。 [技能設定] 索引標籤和 [技能 JSON 編輯器] 允許直接編輯技能。 [`</>`](#expression-evaluator) 會開啟一個視窗，供您用來檢視和編輯技能輸入與輸出的運算式。

[技能設定] 視窗中的巢狀輸入控制項可用來建立投影的複雜圖表、複雜類型欄位的輸出欄位對應，或是技能的輸入。 搭配 [運算式評估工具] 使用時，巢狀輸入會提供簡單的測試及驗證運算式建立器。

## <a name="skill-execution-history"></a>技能執行歷程記錄

技能可以在單一文件的技能中執行多次。 例如，OCR 技能會針對每個文件中解壓縮的每個映像執行一次。 在 [技能詳細資料] 窗格中，[執行] 索引標籤會顯示技能的執行歷程記錄，讓您深入了解技能的每個引動過程。 

執行歷程記錄可讓您追蹤特定擴充回到產生該擴充的技能。 按一下技能輸入，會導覽至產生該輸入的技能。 這可以讓您識別下游技能中可能會造成問題的根本原因。 

當發現潛在的問題時，執行歷程記錄會顯示產生特定輸入之技能的連結，並提供類似功能的堆疊追蹤。 按一下與輸入相關聯的技能，導覽至用來修正任何錯誤的技能，或繼續追蹤特定的問題。

當您建立自訂技能或使用自訂技能針對錯誤進行偵錯時，可以選擇在執行歷程記錄中產生技能引動過程的要求。

## <a name="enriched-data-structure"></a>豐富型資料結構

[豐富型資料結構] 窗格會透過技能顯示文件的擴充，詳述每個擴充的內容和原始技能。 [運算式評估工具] 也可以用來檢視每個擴充的內容。

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="建立偵錯工作階段":::

## <a name="expression-evaluator"></a>運算式評估工具

[運算式評估工具] 可讓您快速查看任何路徑的值。 其可讓您在更新技能或投影的任何輸入或內容之前，編輯路徑及測試結果。

## <a name="errorswarnings"></a>錯誤/警告

這個視窗會顯示技能在偵錯工作階段中針對文件執行時所產生的所有錯誤和警告。

## <a name="limitations"></a>限制

Debug sesisons 可與所有正式運作的資料來源搭配使用，amd 大部分的預覽資料來源。 目前不支援 (預覽版的 MongoDB API) 和 Cassandra API (preview) 。

## <a name="next-steps"></a>後續步驟

既然您已了解偵錯工作階段的元素，請嘗試教學課程以取得實際操作體驗。

> [!div class="nextstepaction"]
> [探索偵錯工作階段功能教學課程](./cognitive-search-tutorial-debug-sessions.md)