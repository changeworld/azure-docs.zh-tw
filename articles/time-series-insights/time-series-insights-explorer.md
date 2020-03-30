---
title: 使用資源管理器流覽資料 - Azure 時間序列見解 |微軟文檔
description: 瞭解如何使用 Azure 時間序列見解資源管理器查看 IoT 資料。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b8c2ba54fcc69ba126bf5f68aed99b25f8156155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046169"
---
# <a name="azure-time-series-insights-explorer"></a>Azure 時間序列深入解析總管

本文介紹 Azure 時間序列見解[資源管理器 Web 應用](https://insights.timeseries.azure.com/)的通用功能和選項。 時序見解資源管理器演示了服務提供的強大資料視覺化功能，可以在您自己的環境中訪問。

Azure 時間序列深入解析是完全受控的分析、儲存體及視覺效果服務，讓您可輕易同時探索及分析數十億筆的 IoT 事件。 它可為您提供資料的全域檢視，從而讓您快速驗證 IoT 解決方案，並避免關鍵任務裝置成本高昂的停機時間。 您可以幾近即時地探索隱藏趨勢、找出異常狀況，並進行根本原因分析。 時間序列深入解析總管目前處於公開預覽狀態。

> [!TIP]
> 有關流覽演示環境的引導教程，請閱讀[Azure 時間序列見解快速入門](time-series-quickstart.md)。

## <a name="video"></a>影片

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>使用時序見解資源管理器瞭解如何查詢資料。 </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>播放前面的視頻<a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"使用 Azure IoT 解決方案加速器開始時間序列見解"。</a>

## <a name="prerequisites"></a>Prerequisites

在使用時間序列深入解析總管之前，您必須先：

- 建立時間序列深入解析環境。 有關詳細資訊，請閱讀[如何開始使用時間序列見解](./time-series-insights-get-started.md)。
- 提供對環境中的帳戶[的存取權限](time-series-insights-data-access.md)。
- 向其添加[IoT 中心](time-series-insights-how-to-add-an-event-source-iothub.md)或[事件中心](time-series-insights-how-to-add-an-event-source-eventhub.md)事件源。

## <a name="explore-and-query-data"></a>探索及查詢資料

在將事件來源連線至時間序列深入解析環境的幾分鐘內，您可以探索並查詢時間序列資料。

1. 首先，在 Web 瀏覽器中打開[時序見解資源管理器](https://insights.timeseries.azure.com/)。 在視窗的左側，選擇環境。 您可以存取的所有環境都會依字母順序列出。

1. 選擇環境後，請使用頂部的 **"從"** 和 **"到"** 配置，或者選擇並拖動所需的時間跨度。 選擇右上角的放大鏡，或按右鍵所選的時間跨度並選擇 **"搜索**"。

1. 您還可以通過選擇 **"自動打開**"按鈕，每分鐘自動刷新可用性。 "**自動打開**"按鈕僅適用于可用性圖表，不適用於主視覺化的內容。

1. Azure 雲圖示將帶您到 Azure 門戶中的環境。

   [![時間序列洞察環境選擇](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. 接下來，將顯示顯示選定時間跨度內所有事件計數的圖表。 這裡您有許多控制項：

    - **術語編輯器面板**：術語空間是您查詢環境的位置。 它在螢幕的左側找到：
      - **測量**： 此下拉清單顯示所有數位列 （**雙列**）。
      - **拆分 BY**： 此下拉清單顯示分類列 （**字串**）。
      - 您可以啟用步長插值，顯示最小值和最大值，並從 **"測量"** 旁邊的控制台調整 y 軸。 您還可以調整顯示的資料是資料的計數、平均值還是總和。
      - 您最多可以添加五個術語，以便在同一 X 軸上查看。 選擇 **"添加"** 以添加新術語或使用 **"克隆此術語"** 按鈕添加現有術語的副本。

        [![術語選擇、篩選和查詢面板](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **謂詞**：使用謂詞使用下表中列出的一組運算元快速篩選事件。 如果通過選擇或按一下進行搜索，謂詞將自動基於該搜索進行更新。 支援的運算元類型包括：

         |作業  |支援的類型  |注意  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **雙**，**日期時間**，**時間跨度**       |         |
         |**=**, **!=**,**<>**     | **字串**，**波爾**，**雙**，**日期時間**，**時間跨度**， **Null**        |         |
         |**在**     | **字串**，**波爾**，**雙**，**日期時間**，**時間跨度**， **Null**        |  所有運算元應為相同類型或**Null**常量。        |
         |**HAS**     | **字串**        |  右側只允許恒定的字串文本。 不允許使用空字串和**Null。**       |

      - **查詢範例**

         [![GA 查詢示例](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. 您可以使用 **"間隔大小"** 滑塊工具在同一時間跨度內放大和縮小間隔。 滑塊提供更精確地控制大切片之間的移動，這些切片顯示平滑趨勢，下至小至毫秒的切片，從而允許您顯示和分析資料的細微性高解析度切割。 滑塊的預設起始點設置為所選資料的最佳視圖，以平衡解析度、查詢速度和細微性。

1. "**時間畫筆"** 工具便於從一個時間跨度導航到另一個時間跨度。

1. 選擇 **"保存**"圖示以保存當前查詢，並將其與環境的其他使用者共用。 選擇 **"打開"** 圖示時，您可以查看您有權訪問的環境中所有已保存的查詢和其他使用者的任何共用查詢。

   [![查詢](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>顯現資料

1. 使用 **"透視視圖"** 工具可同時查看最多四個唯一查詢。 **"透視視圖"** 按鈕位於圖表的右上角。

   [![選擇要添加到透視窗格的查詢](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. 查看圖表以直觀地流覽資料，並使用**圖表**工具：

    - **選擇**或**按一下**特定時間跨度或單個資料數列。
    - 在時間跨度選擇中，您可以縮放或流覽事件。
    - 在資料序列內，您可以由另一個資料行分割序列、將序列新增為新的字詞、只顯示選取的序列、排除選取的序列、釘選該序列或從選取的序列探索事件。
    - 在圖表左側的篩選器區域中，您可以查看所有顯示的資料數列，然後按值或名稱重新排序。 您還可以查看所有資料數列或任何固定或未固定的系列。 您可以選擇單個資料數列，並將系列拆分為另一列，將該系列添加為新術語，僅顯示所選系列，排除所選系列，固定該系列，或流覽所選系列中的事件。
    - 同時查看多個術語時，可以堆疊、取消堆疊、查看有關資料數列的其他資料，並在所有術語中使用相同的 y 軸。 使用圖表右上角的按鈕。

    [![圖表工具右上角選項設置](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. 使用**熱圖**快速發現給定查詢中的唯一或異常資料數列。 只有一個搜尋字詞可視覺化為熱度圖。

    [![GA 資源管理器熱圖圖](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. 當您通過選擇或按右鍵來流覽事件時，"**事件"** 面板可用。 在這裡，您可以查看所有原始事件，並將事件匯出為 JSON 或 CSV 檔。 時間序列見解存儲所有原始資料。

    [![事件](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. 在流覽事件以公開模式和列統計資訊後，選擇 **"統計資訊"** 選項卡。

    - **模式**：此功能主動顯示選定資料區域中最具統計意義的模式。 您不必查看數千個事件，瞭解哪些模式最需要時間和精力。 借助時序見解，您可以直接跳轉到這些具有統計顯著性的模式，以繼續進行分析。 這項功能對於事後調查歷史資料也很有幫助。
    - **列統計資訊**： 列統計資訊提供圖表和表格，用於細分所選資料數列在選定時間跨度內每個列的資料。

      [![STATS 直條圖表和選項](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

現在，您已經瞭解了時序見解資源管理器 Web 應用中提供的關鍵功能、配置設置和顯示選項。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[診斷和解決](time-series-insights-diagnose-and-solve-problems.md)時間序列見解環境中的問題。

- 參加引導的[Azure 時間序列見解快速入門](time-series-quickstart.md)教程。
