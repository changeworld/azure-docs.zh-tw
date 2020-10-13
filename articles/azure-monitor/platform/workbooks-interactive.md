---
title: 使用自訂參數 Azure 監視器活頁簿
description: 使用預先建立及使用自訂參數之活頁簿來簡化複雜的報告
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: ee71082b96d41f9bad7fb54a70d308c24dec5b57
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932034"
---
# <a name="interactive-workbooks"></a>互動式活頁簿

活頁簿可讓作者為其取用者建立互動式報表和體驗。 支援多種方式的互動。

## <a name="parameter-changes"></a>參數變更

當活頁簿使用者更新參數時，任何使用該參數的控制項都會自動重新整理並重新繪製，以反映新的狀態。 這是大部分的 Azure 入口網站報表支援互動性的方式。 活頁簿以簡單的使用者工作方式提供這項功能。

深入瞭解活頁 [簿中的參數](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>方格、圖格、圖表選取專案

活頁簿可讓作者建立案例，其中按一下方格中的資料列會根據資料列的內容來更新後續圖表。

例如，使用者可以有一個方格，其中顯示要求清單和某些統計資料（例如失敗計數）。 他們可以將其設定為按一下對應于要求的資料列，將會產生如下的詳細圖表更新，以篩選出該要求。

### <a name="setting-up-interactivity-on-grid-row-click"></a>在方格資料列按一下時設定互動

1. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [ _加入查詢_ ] 連結可將記錄查詢控制項加入至活頁簿。
3. 選取 [ _記錄_]、[資源類型] 作為 [查詢類型] (例如 Application Insights) 以及要作為目標的資源。
4. 使用查詢編輯器來輸入您分析的 KQL

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query` 查看結果
6. 選取查詢頁尾上的 [ _Advanced Settings_ ] 圖示 (圖示看起來像齒輪) 。 這會開啟 [advanced settings] 窗格。
7. 檢查設定： `When an item is selected, export a parameter` 。
8. 在您選取的設定下，選取 [ *加入參數* ]，並填寫下列資訊。
    1. 要匯出的欄位： `Request`
    2. 參數名稱： `SelectedRequest`
    3. 預設值：`All requests`
9. 選取 [儲存]

    ![顯示 [advanced 編輯器] 的螢幕擷取畫面，其中包含將欄位匯出為參數的設定。](./media/workbooks-interactive/export-parameters-add.png)

10. 選取 `Done Editing`。
11. 使用步驟2和3來新增另一個查詢控制項。
12. 使用查詢編輯器來輸入您分析的 KQL。
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` 以查看結果。
14. 將 _視覺效果_ 變更為 `Area chart` 。
15. 選擇要在第一個方格中選取的資料列。 請注意下方的區域圖如何篩選至選取的要求。

產生的報表在編輯模式中看起來像這樣：

![編輯模式中前兩個查詢的螢幕擷取畫面。](./media/workbooks-interactive//interactivity-grid-create.png)

下圖根據相同的原則，以讀取模式顯示更詳盡的互動式報表。 報表會使用方格按一下來匯出參數，而這些參數會在兩個圖表和一個文字區塊中使用。

![在讀取模式中使用方格按一下的報表螢幕擷取畫面。](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>匯出整個資料列的內容

有時候，匯出所選取資料列的整個內容，而不只是特定資料行，是很理想的做法。 在這種情況下，請將 `Field to export` 屬性保留在上述步驟7.1 中。 活頁簿會將整個資料列內容以 json 格式匯出至參數。

在參考的 KQL 控制項上，使用 `todynamic` 函數來剖析 json 並存取個別資料行。

## <a name="grid-cell-clicks"></a>方格資料格按一下

活頁簿可讓作者透過一種特殊類型的方格資料行轉譯器（稱為）來新增互動 `link renderer` 。 連結轉譯器會根據資料格的內容，將方格資料格轉換成超連結。 活頁簿支援多種連結轉譯器，包括允許開啟資源總覽 blade、屬性包檢視器、應用程式見解搜尋、使用量、交易追蹤等專案。

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>使用方格資料格點擊設定互動

1. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [ _加入查詢_ ] 連結可將記錄查詢控制項加入至活頁簿。
3. 選取 [ _記錄_]、[資源類型] 作為 [查詢類型] (例如 Application Insights) 以及要作為目標的資源。
4. 使用查詢編輯器來輸入您分析的 KQL

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query` 查看結果
6. 選取 [資料 _行設定_ ] 以開啟 [設定] 窗格。
7. 在 [資料 _行_ ] 區段中，設定：
    1. _範例_ -資料行轉譯器： `Link` 、要開啟的視圖： `Cell Details` 、連結標籤： `Sample`
    2. _計數_ -資料行轉譯器： `Bar` ，調色板： `Blue` ，最小值： `0`
    3. _要求_ 資料行轉譯器： `Automatic`
    4. 選取 [ _儲存並關閉_ ] 以套用變更

    ![[資料行設定] 索引標籤的螢幕擷取畫面。](./media/workbooks-interactive/column-settings.png)

8. 按一下方格中的其中一個 `Sample` 連結。 這會開啟一個窗格，其中包含取樣要求的詳細資料。

    ![取樣要求的詳細資料窗格螢幕擷取畫面。](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>連結轉譯器動作

| 連結動作 | 按一下時的動作 |
|:------------- |:-------------|
| `Generic Details` | 顯示內容方格內容索引標籤中的資料列值 |
| `Cell Details` | 顯示內容方格內容索引標籤中的資料格值。當儲存格包含具有資訊 (的動態型別時很有用，例如，具有位置、角色實例等要求屬性的 json ) 。 |
| `Cell Details` | 顯示內容方格內容索引標籤中的資料格值。當儲存格包含具有資訊 (的動態型別時很有用，例如，具有位置、角色實例等要求屬性的 json ) 。 |
| `Custom Event Details` | 開啟 Application Insights 搜尋詳細資料，其中包含自訂事件識別碼 (資料 `itemId` 格中的)  |
| `* Details` | 類似于自訂事件詳細資料，但相依性、例外狀況、網頁檢視、要求和追蹤除外。 |
| `Custom Event User Flows` | 開啟資料格內自訂事件名稱的 Application Insights 消費者流程體驗 |
| `* User Flows` | 類似于自訂事件消費者流程例外狀況、頁面流覽和要求除外 |
| `User Timeline` | 開啟使用者識別碼 (user_Id) 在資料格中的使用者時間軸 |
| `Session Timeline` | 開啟儲存格中值的 Application Insights 搜尋體驗 (例如，搜尋文字 ' abc '，其中 abc 是資料格中的值)  |
| `Resource overview` | 根據資料格中的資源識別碼值，在入口網站中開啟資源的總覽 |

## <a name="conditional-visibility"></a>條件式可見度

活頁簿可讓使用者根據參數的值，讓特定的控制項出現或消失。 這可讓作者根據使用者輸入或遙測狀態，讓報表看起來不同。 其中一個範例是當事物很好，但在發生問題時顯示完整詳細資料時，只會顯示取用者的摘要。

### <a name="setting-up-interactivity-using-conditional-visibility"></a>使用條件式可見度設定互動

1. 遵循 [在方格資料 [列上設定互動](#setting-up-interactivity-on-grid-row-click) ] 區段中的步驟，設定兩個互動式控制項。
2. 在頂端加入新的參數：
    1. 名稱：`ShowDetails`
    2. 參數類型： `Drop down`
    3. 必填： `checked`
    4. 取得資料來源： `JSON`
    5. JSON 輸入： `["Yes", "No"]`
    6. 儲存以認可變更。

    ![選取 [加入參數] 按鈕之後，就會顯示 [編輯參數] 窗格。](./media/workbooks-interactive/edit-parameter.png)

3. 將參數值設定為 `Yes`

    ![在 [完成編輯] 按鈕上方是一個下拉式清單，可讓您設定參數值](./media/workbooks-interactive/set-parameter.png)

4. 在含有區域圖的查詢控制項中，選取 [ _Advanced Settings_ ] 圖示 (齒輪圖示) 
5. 檢查設定 `Make this item conditionally visible`
    1. 如果 `ShowDetails` 參數值 `equals` 為，就會顯示此專案 `Yes`
6. 選取 [ _完成編輯_ ] 以認可變更。
7. 選取 [活頁簿] 工具列上的 [ _完成編輯_ ] 以進入讀取模式。
8. 將參數的值切換 `ShowDetails` 為 `No` 。 請注意，下圖已消失。

下圖顯示可見的案例，其中 `ShowDetails` 是 `Yes`

![顯示可顯示圖表的條件式可見度的螢幕擷取畫面](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

下圖顯示隱藏的案例，其中 `ShowDetails` 是 `No`

![顯示隱藏圖表之條件式可見度的螢幕擷取畫面](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>格線和圖表中多重選取範圍的互動

當選取了資料列 (或多個資料列) 時，查詢和計量步驟也可以匯出一或多個參數。

![顯示具有多個參數之匯出參數設定的螢幕擷取畫面。 ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. 在顯示方格的查詢步驟中，移至 [advanced] 設定。
2. 核取此複選 `When items are selected, export parameters` 框。 其他控制項將會出現。
3. 核取此複選 `allow selection of multiple values` 框。
    1. 顯示的視覺效果將允許多重選取和匯出的參數值為值的陣列，例如使用多重選取的下拉式參數時。
    2. 如果未選取，則顯示視覺效果只會考慮最後選取的專案。 一次只匯出單一值。
4. 針對您想要匯出的每個參數，使用 [ *加入參數* ] 按鈕。 快顯視窗隨即出現，其中包含要匯出之參數的設定。

啟用單一選取時，作者可以指定要匯出的原始資料欄位。 如果未選取任何專案，則欄位包括參數名稱、參數類型和預設值 (選擇性) 。

啟用多重選取時，作者會指定要匯出的原始資料欄位。 欄位包括參數名稱、參數類型、具有和分隔符號的引號。 當您在查詢中被取代時，將箭號值轉換為文字時，會使用包含和分隔符號值的引號。 如果未選取任何值，則在多重選取中，預設值為空陣列。

> [!NOTE]
> 若為多重選取，則只會匯出唯一值，您不會看到輸出陣列值（例如 "1，1，2，1"）您會收到 "1，2" 作為輸出值。

您可以將匯出設定中的 [要匯出的欄位] 設定保留空白。 如果您這樣做，資料中的所有可用欄位都將匯出為索引鍵：值組的 stringified JSON 物件。 針對方格和標題，這會是方格中的所有欄位。 若為圖表，可用的欄位將會是 x、y、數列和 label (取決於圖表的類型) 。

雖然預設行為是以文字形式匯出參數，但如果您知道該欄位是訂用帳戶或資源識別碼，請使用該欄位作為匯出參數類型。 這可讓參數在需要這些參數類型的位置中使用下游。

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md) 瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md) 和共用活頁簿資源的存取權。
