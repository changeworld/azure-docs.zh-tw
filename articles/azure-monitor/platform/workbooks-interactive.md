---
title: 使用自訂參數 Azure 監視器活頁簿
description: 使用預先建立及使用自訂參數之活頁簿來簡化複雜的報告
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658263"
---
# <a name="interactive-workbooks"></a>互動式活頁簿

活頁簿可讓作者為其取用者建立互動式報表和體驗。 有數種方式可支援互動性。

## <a name="parameter-changes"></a>參數變更
當活頁簿使用者更新參數時，任何使用參數的控制項都會自動重新整理和重新繪製，以反映新的狀態。 這是大部分的 Azure 入口網站報表支援互動的方式。 活頁簿以非常簡單的方式，以最少的使用者工作來提供這項功能。

深入瞭解活頁[簿中的參數](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>方格資料列點擊
活頁簿可讓作者建立案例，其中按一下方格中的資料列會根據資料列的內容來更新後續圖表。 

例如，使用者可以有一個方格，其中顯示要求清單和一些統計資料，例如失敗計數。 他們可以將其設定為按一下對應于要求的資料列，將會產生以下詳細的圖表，並將其更新為僅篩選出該要求。

### <a name="setting-up-interactivity-on-grid-row-click"></a>設定方格資料列的互動
1. 按一下 [_編輯_] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [_加入查詢_] 連結，將記錄查詢控制項加入至活頁簿。 
3. 選取 [_記錄_]、[資源類型] （例如 Application Insights）和 [目標資源] 做為 [查詢類型]。
4. 使用查詢編輯器來輸入分析的 KQL
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`查看結果
6. 按一下查詢頁尾上的 [_高級設定_] 圖示（圖示看起來就像齒輪）。 這會開啟 [高級設定] 窗格 
7. 檢查設定：`When an item is selected, export a parameter`
    1. 要匯出的欄位：`Request`
    2. 參數名稱：`SelectedRequest`
    3. 預設值：`All requests`
    
    ![顯示 [高級編輯器] 的影像，其中包含將欄位匯出為參數的設定](./media/workbooks-interactive/advanced-settings.png)

8. 按一下 [ `Done Editing`]。
9. 使用步驟2和3加入另一個查詢控制項。
10. 使用查詢編輯器來輸入分析的 KQL
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`以查看結果。
12. 將_視覺效果_變更為`Area chart`
12. 按一下第一個方格中的資料列。 請注意下方的區域圖如何篩選為選取的要求。

產生的報表在編輯模式中看起來像這樣：

![顯示建立互動式體驗的影像使用方格資料列點擊](./media/workbooks-interactive//grid-click-create.png)

下圖根據相同的原則，在閱讀模式中顯示更詳盡的互動式報表。 此報表會使用方格點按來匯出參數，然後在兩個圖表和文字區塊中使用。

![顯示建立互動式體驗的影像使用方格資料列點擊](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>匯出整個資料列的內容
有時候，您可能會想要匯出所選資料列的整個內容，而不只是特定資料行。 在這種情況下，請在 `Field to export` 上述步驟7.1 中將屬性保留為未設定。 活頁簿會將整個資料列內容以 json 格式匯出至參數。 

在參考的 KQL 控制項上，使用函式 `todynamic` 來剖析 json 並存取個別的資料行。

 ## <a name="grid-cell-clicks"></a>方格資料格點擊
活頁簿可讓作者透過特殊類型的格線資料行轉譯器（稱為）來新增互動 `link renderer` 。 連結轉譯器會根據儲存格的內容，將方格資料格轉換成超連結。 活頁簿支援多種類型的連結轉譯器，包括可開啟資源總覽 blade、屬性包檢視器、App Insights 搜尋、使用量、交易追蹤等等的範本轉譯器。

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>使用方格資料格點按滑鼠來設定互動性
1. 按一下 [_編輯_] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [_加入查詢_] 連結，將記錄查詢控制項加入至活頁簿。 
3. 選取 [_記錄_]、[資源類型] （例如 Application Insights）和 [目標資源] 做為 [查詢類型]。
4. 使用查詢編輯器來輸入分析的 KQL
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`查看結果
6. 按一下 [資料_行設定_] 以開啟 [設定] 窗格。
7. 在 [資料_行_] 區段中，設定：
    1. _範例_-資料行轉譯器： `Link` ，View 可開啟： `Cell Details` ，連結標籤：`Sample`
    2. _計數_-資料行轉譯器： `Bar` ，色彩色板： `Blue` ，最小值：`0`
    3. _要求_-資料行轉譯器：`Automatic`
    4. 按一下 [_儲存並關閉_] 以套用變更
8. 按一下方格中的其中一個 `Sample` 連結。 這會開啟 [屬性] 窗格，其中包含取樣要求的詳細資料。

    ![顯示如何使用方格資料格來建立互動式體驗的影像](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>連結轉譯器動作
| 連結動作 | 按一下時的動作 |
|:------------- |:-------------|
| `Generic Details` | 顯示內容方格內容分頁中的資料列值 |
| `Cell Details` | 顯示內容方格內容分頁中的資料格值。 當資料格包含具有資訊的動態型別（例如，具有位置、角色實例等要求屬性的 json）時，很有用。 |
| `Cell Details` | 顯示內容方格內容分頁中的資料格值。 當資料格包含具有資訊的動態型別（例如，具有位置、角色實例等要求屬性的 json）時，很有用。 |
| `Custom Event Details` | 使用資料格內的自訂事件識別碼（itemId），開啟 Application Insights 搜尋詳細資料 |
| `* Details` | 類似于自訂事件詳細資料，但相依性、例外狀況、頁面流覽、要求和追蹤除外。 |
| `Custom Event User Flows` | 開啟在資料格的自訂事件名稱上進行切換的 Application Insights 消費者流程體驗 |
| `* User Flows` | 類似于自訂事件消費者流程例外狀況、頁面流覽和要求除外 |
| `User Timeline` | 以使用者識別碼（user_Id）在資料格中開啟使用者時間軸 |
| `Session Timeline` | 針對資料格中的值開啟 [Application Insights 搜尋體驗] （例如，搜尋文字 ' abc '，其中 abc 是資料格中的值） |
| `Resource overview` | 根據資料格中的資源識別碼值，在入口網站中開啟資源的總覽 |

## <a name="conditional-visibility"></a>條件式可見度
活頁簿可讓使用者根據參數值來顯示或消失特定的控制項。 這可讓作者根據使用者輸入或遙測狀態，讓報表看起來不同。 例如，當事情良好時，取用者只會顯示摘要，但在發生錯誤時，會顯示完整的詳細資料。

### <a name="setting-up-interactivity-using-conditional-visibility"></a>使用條件式可見度設定互動性
1. 請依照一節中的步驟 `Setting up interactivity on grid row click` 來設定兩個互動式控制項。
2. 在頂端加入新的參數：
    1. 名稱：`ShowDetails`
    2. 參數類型：`Drop down`
    3. 必填：`checked`
    4. 取得資料來源：`JSON`
    5. JSON 輸入：`["Yes", "No"]`
    6. 儲存以認可變更。
3. 將參數值設定為`Yes`
4. 在含有區域圖的查詢控制項中，按一下 [_高級設定_] 圖示（齒輪圖示）
5. 檢查設定`Make this item conditionally visible`
    1. 如果 `ShowDetails` 參數值 `equals` 為，則會顯示此專案`Yes`
6. 按一下 [_完成編輯_] 以認可變更。
7. 按一下活頁簿工具列上的 [_完成編輯_] 以進入 [讀取] 模式。
8. 將參數的值切換 `ShowDetails` 為 `No` 。 請注意，下圖會消失。

下圖顯示的是可見的情況，其中 `ShowDetails` 是`Yes`

![顯示可顯示圖表之條件式可見度的影像](./media/workbooks-interactive/conditional-visibility.png)

下圖顯示的是隱藏的案例，其中 `ShowDetails` 是`No`

![顯示隱藏圖表之條件式可見度的影像](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>後續步驟


* [開始深入](workbooks-visualizations.md)瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md)和共用您的活頁簿資源的存取權。
