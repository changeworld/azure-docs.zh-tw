---
title: Azure 監視器活頁簿磚的視覺效果
description: 瞭解所有 Azure 監視器活頁簿磚的視覺效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663993"
---
# <a name="tile-visualizations"></a>磚視覺效果

磚是在活頁簿中呈現摘要資料的實用方式。 下圖顯示圖格的常見使用案例，其中包含詳細方格頂端的應用層級摘要。

[![磚摘要視圖的螢幕擷取畫面](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

活頁簿磚支援顯示標題、子標題、大型文字、圖示、以度量為基礎的漸層、spark 線/橫條、頁尾等等。

## <a name="adding-a-tile"></a>新增磚

1. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
2. 選取 [ **加入** ]，然後 *加入 [查詢* ]，將記錄查詢控制項加入至活頁簿。
3. 選取 [ **記錄**]、[資源類型] 作為 [查詢類型] (例如 Application Insights) 以及要作為目標的資源。
4. 使用查詢編輯器來輸入您分析的 KQL。

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. 將 [大小] 設定為 [ **完整**]。
6. 將視覺效果設定為 **圖**格。
7. 選取 [ **磚設定** ] 按鈕以開啟 [設定] 窗格。
    1. 在 [ *標題*] 中，設定：
        * 使用資料行： `name` 。
    2. 在 *左邊*，設定：
        * 使用資料行： `Requests` 。
        * 資料行轉譯器： `Big Number` 。
        * 調色板： `Green to Red`
        * 最小值： `0` 。
    3. 在 *底部*，設定：
        * 使用資料行： `appName` 。
8. 選取窗格底部的 [ **儲存後關閉** ] 按鈕。

[![螢幕擷取畫面：具有上述查詢和磚設定的磚摘要視圖。](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

處於讀取模式的磚：

[![以讀取模式顯示磚摘要視圖的螢幕擷取畫面。](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>磚中的 Spark 線條

1. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
2. 加入名為的時間範圍參數 `TimeRange` 。
    1. 選取 [ **新增** ]，然後 *新增參數*。
    2. 在 [參數] 控制項中，選取 [ **加入參數**]。
    3. `TimeRange`在 [*參數名稱*] 欄位中輸入，然後選擇 [做 `Time range picker` 為*參數類型*]。
    4. 選取窗格頂端的 [ **儲存** ]，然後選取 [在參數控制項中 **完成編輯** ]。
3. 選取 [ **新增** ]，然後 *新增 [查詢* ]，將記錄查詢控制項新增至參數控制項底下。
4. 選取 [ **記錄**]、[資源類型] 作為 [查詢類型] (例如 Application Insights) 以及要作為目標的資源。
5. 使用查詢編輯器來輸入您分析的 KQL。

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. 選取 [執行查詢]****。  (請務必在執行 `TimeRange` 查詢之前，先將設定為您選擇的值。 ) 
7. 將 *視覺效果* 設定為「磚」。
8. 將 [ *大小* ] 設定為 [完整]。
9. 選取 [ **磚設定**]。
    1. 在 *磚*中，設定：
        * 使用資料行： `name` 。
    2. 在 *Subtile*中，設定：
        *  使用資料行： `appNAme` 。
    3. 在 *左邊*，設定：
        *  使用資料行： `Requests` 。
        * 資料行轉譯器： `Big Number` 。
        * 色調色板： `Green to Red` 。
        * 最小值： `0` 。
    4. 在 *底部*，設定：
        * 使用資料行： `Trend` 。
        * 資料行轉譯器： `Spark line` 。
        * 色調色板： `Green to Red` 。
        * 最小值： `0` 。
10. 選取窗格底部的 [ **儲存並關閉** ]。

![具有 spark 線之磚視覺效果的螢幕擷取畫面](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>磚大小

作者有選項可設定磚設定中的圖格寬度。

* `fixed` (預設)

    磚的預設行為是相同的固定寬度、大約160圖元寬，加上磚周圍的空間。

    ![顯示固定寬度圖格的螢幕擷取畫面](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    每個標題都會縮減或成長以容納其內容，不過，磚會限制為圖格視圖的寬度， (不會有水準滾動) 。

    ![顯示自動寬度圖格的螢幕擷取畫面](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    每個標題一律會是磚寬度的全形，每行一個標題。

     ![顯示全尺寸寬度圖格的螢幕擷取畫面](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>接下來的步驟

* 磚也支援複合條形轉譯器。 若要深入瞭解，請造訪 [複合 Bar 檔](workbooks-composite-bar.md)。
* 若要深入瞭解時間參數，例如 `TimeRange` 造訪活頁 [簿時間參數檔](workbooks-time.md)。