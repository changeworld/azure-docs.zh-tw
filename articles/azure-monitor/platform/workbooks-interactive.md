---
title: Azure 監視具有自訂參數的活頁簿
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658263"
---
# <a name="interactive-workbooks"></a>互動式活頁簿

活頁簿允許作者為其消費者創建互動式報告和體驗。 交互性在許多方面得到支援。

## <a name="parameter-changes"></a>參數更改
當活頁簿使用者更新參數時，使用 該參數的任何控制項都會自動刷新和重繪以反映新狀態。 這是大多數 Azure 門戶報告都支援交互性的方式。 活頁簿以非常直接的方式提供這一點，使用者工作量最小。

在[活頁簿中瞭解有關參數的更多詳細資訊](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>網格行按一下
活頁簿允許作者構造方案，其中按一下網格中的行會根據行的內容更新後續圖表。 

例如，使用者可以有一個網格，顯示請求清單和某些統計資訊（如失敗計數）。 他們可以設置它，以便按一下與請求對應的行，將導致下面的詳細圖表更新以篩選到僅該請求。

### <a name="setting-up-interactivity-on-grid-row-click"></a>設置網格行上的交互性按一下
1. 通過按一下 _"編輯_"工具列項，將活頁簿切換到編輯模式。
2. 使用 _"添加"查詢_連結將日誌查詢控制項添加到活頁簿。 
3. 選取查詢類型作為_日誌_、資源類型（例如，應用程式見解）和目標資源。
4. 使用查詢編輯器輸入 KQL 進行分析
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`查看結果
6. 按一下查詢頁腳上的 _"高級設置"_ 圖示（該圖示看起來像齒輪）。 這將打開高級設置窗格 
7. 檢查設置：`When an item is selected, export a parameter`
    1. 要匯出的欄位：`Request`
    2. 參數名稱：`SelectedRequest`
    3. 預設值：`All requests`
    
    ![顯示高級編輯器的圖像，其中設置將欄位匯出為參數](./media/workbooks-interactive/advanced-settings.png)

8. 按一下 [ `Done Editing`]。
9. 使用步驟 2 和步驟 3 添加另一個查詢控制項。
10. 使用查詢編輯器輸入 KQL 進行分析
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`以查看結果。
12. 將_視覺化更改為_`Area chart`
12. 按一下第一個網格中的行。 請注意下面的區域圖如何篩選到所選請求。

生成的報表在編輯模式下如下所示：

![顯示使用網格行按一下創建互動式體驗的圖像](./media/workbooks-interactive//grid-click-create.png)

下圖顯示了基於相同原則的讀取模式下更精細的互動式報表。 報表使用網格按一下來匯出參數 - 這些參數又用於兩個圖表和一個文字區塊。

![顯示使用網格行按一下創建互動式體驗的圖像](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>匯出整行的內容
有時需要匯出所選行的全部內容，而不僅僅是特定列。 在這種情況下，保留上述`Field to export`步驟 7.1 中未設置的屬性。 活頁簿將整個行內容作為 json 匯出到參數。 

在引用 KQL 控制項上，使用`todynamic`函數解析 json 並訪問各個列。

 ## <a name="grid-cell-clicks"></a>網格儲存格按一下
活頁簿允許作者通過稱為 的特殊類型的網格列呈現器添加交互性`link renderer`。 連結渲染器根據儲存格的內容將網格儲存格轉換為超連結。 活頁簿支援多種連結呈現器 - 包括允許打開資源概述刀片、屬性包檢視器、應用見解搜索、使用方式、事務跟蹤等的功能。

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>使用網格儲存格按一下設置交互性
1. 通過按一下 _"編輯_"工具列項，將活頁簿切換到編輯模式。
2. 使用 _"添加"查詢_連結將日誌查詢控制項添加到活頁簿。 
3. 選取查詢類型作為_日誌_、資源類型（例如，應用程式見解）和目標資源。
4. 使用查詢編輯器輸入 KQL 進行分析
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`查看結果
6. 按一下 _"列設置"_ 以打開設置窗格。
7. 在 _"列"_ 部分中，設置：
    1. _示例_- 列渲`Link`染器：、查看`Cell Details`打開： 、連結標籤：`Sample`
    2. _計數_- 列渲`Bar`染器：、`Blue`調色板： 、最小值：`0`
    3. _請求_- 列渲染器：`Automatic`
    4. 按一下"_保存"和"關閉_"以應用更改
8. 按一下網格中的`Sample`一個連結。 這將打開一個包含採樣請求詳細資訊的屬性窗格。

    ![顯示使用網格儲存格按一下創建互動式體驗的圖像](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>連結渲染器操作
| 連結操作 | 按一下操作 |
|:------------- |:-------------|
| `Generic Details` | 在屬性網格上下文邊欄選項卡中顯示行值 |
| `Cell Details` | 在屬性網格上下文邊欄選項卡中顯示儲存格值。 當儲存格包含包含包含資訊的動態類型（例如，具有請求屬性（如位置、角色實例等）的 json 時非常有用。 |
| `Cell Details` | 在屬性網格上下文邊欄選項卡中顯示儲存格值。 當儲存格包含包含包含資訊的動態類型（例如，具有請求屬性（如位置、角色實例等）的 json 時非常有用。 |
| `Custom Event Details` | 使用儲存格中的自訂事件 ID （itemId） 打開應用程式見解搜索詳細資訊 |
| `* Details` | 與自訂事件詳細資訊類似，但依賴項、異常、網頁檢視、請求和跟蹤除外。 |
| `Custom Event User Flows` | 打開在儲存格中的自訂事件名稱上旋轉的應用程式見解使用者流體驗 |
| `* User Flows` | 與自訂事件使用者流類似，例外、網頁檢視和請求除外 |
| `User Timeline` | 在儲存格中使用使用者 ID （user_Id） 打開使用者時間表 |
| `Session Timeline` | 打開儲存格中值的應用程式見解搜索體驗（例如，搜索 abc 中的值的 abc 文本"abc" |
| `Resource overview` | 根據儲存格中的資源識別碼 值在門戶中打開資源概覽 |

## <a name="conditional-visibility"></a>可見度條件
活頁簿允許使用者根據參數的值顯示或消失某些控制項。 這允許作者根據使用者輸入或遙測狀態使報表看起來不同。 例如，當情況良好時，向消費者顯示摘要，但當出現問題時顯示全部詳細資訊。

### <a name="setting-up-interactivity-using-conditional-visibility"></a>使用可見度條件設置交互性
1. 按照本節中`Setting up interactivity on grid row click`的步驟設置兩個互動式控制項。
2. 在頂部添加新參數：
    1. 名稱：`ShowDetails`
    2. 參數類型：`Drop down`
    3. 必填：`checked`
    4. 從：`JSON`
    5. JSON 輸入：`["Yes", "No"]`
    6. 保存以提交更改。
3. 將參數值設置為`Yes`
4. 在帶有面積圖的查詢控制項中，按一下 _"高級設置"_ 圖示（齒輪圖示）
5. 檢查設置`Make this item conditionally visible`
    1. 如果`ShowDetails`參數值`equals`可見此項`Yes`
6. 按一下 _"完成編輯_"以提交更改。
7. 按一下作業簿工具列上的 _"完成編輯_"以進入讀取模式。
8. 將參數`ShowDetails`的值切換到`No`。 請注意，下面的圖表將消失。

下圖顯示了可見大小寫`ShowDetails``Yes`

![顯示圖表可見可見度條件的圖像](./media/workbooks-interactive/conditional-visibility.png)

下圖顯示了隱藏大小寫`ShowDetails``No`

![顯示圖表隱藏位置的可見度條件的圖像](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>後續步驟


* [開始](workbooks-visualizations.md)瞭解有關活頁簿的許多豐富視覺化選項的詳細資訊。
* [控制和](workbooks-access-control.md)共用對活頁簿資源的存取權限。
