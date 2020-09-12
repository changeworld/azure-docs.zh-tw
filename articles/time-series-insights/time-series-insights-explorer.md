---
title: 使用 Explorer Azure 時間序列深入解析探索資料 |Microsoft Docs
description: 瞭解如何使用 Azure 時間序列深入解析 Explorer 來查看您的 IoT 資料。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1bcf8ec2395dbc94154072c1c4d839bf45125a3e
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487295"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure 時間序列深入解析 Gen1 Explorer

本文說明 Azure 時間序列深入解析 Gen1 [Explorer web 應用程式](https://insights.timeseries.azure.com/)的功能和選項。 Azure 時間序列深入解析 Explorer 會示範服務提供的強大資料視覺效果功能，並可在您自己的環境中存取。

Azure 時間序列深入解析是完全受控的分析、儲存體及視覺效果服務，讓您可輕易同時探索及分析數十億筆的 IoT 事件。 它可為您提供資料的全域檢視，從而讓您快速驗證 IoT 解決方案，並避免關鍵任務裝置成本高昂的停機時間。 您可以幾近即時地探索隱藏趨勢、找出異常狀況，並進行根本原因分析。

> [!TIP]
> 如需示範環境的導覽，請閱讀 [Azure 時間序列深入解析快速入門](time-series-quickstart.md)。

## <a name="video"></a>影片

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>瞭解如何使用 Azure 時間序列深入解析 Explorer 來查詢資料。 </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>播放先前的影片「 <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">使用 Azure IoT 解決方案加速器開始 Azure 時間序列深入解析」。</a>

## <a name="prerequisites"></a>必要條件

在您可以使用 Azure 時間序列深入解析 Explorer 之前，您必須：

- 建立 Azure 時間序列深入解析環境。 如需詳細資訊，請參閱 [如何開始使用 Azure 時間序列深入解析](./time-series-insights-get-started.md)。
- 在環境中提供您帳戶的[存取權](time-series-insights-data-access.md)。
- 在其中新增 [IoT 中樞](time-series-insights-how-to-add-an-event-source-iothub.md) 或 [事件中樞](time-series-insights-how-to-add-an-event-source-eventhub.md) 的事件來源。

## <a name="explore-and-query-data"></a>探索及查詢資料

在將事件來源連線到 Azure 時間序列深入解析環境的幾分鐘內，您就可以探索和查詢您的時間序列資料。

1. 若要開始，請在網頁瀏覽器中開啟 [Azure 時間序列深入解析 Explorer](https://insights.timeseries.azure.com/) 。 在視窗的左側，選取環境。 您可以存取的所有環境都會依字母順序列出。

1. 選取環境之後，請使用頂端的 [ **開始** ] 和 [ **到** ] 設定，或選取並拖曳您想要的時間範圍。 選取右上角的放大鏡，或以滑鼠右鍵按一下選取的 timespan，然後選取 [ **搜尋**]。

1. 您也可以選取 [ **自動開啟** ] 按鈕，每分鐘自動重新整理可用性。 [ **自動開啟** ] 按鈕只適用于可用性圖表，而非主要視覺效果的內容。

1. Azure 雲端圖示會帶您前往 Azure 入口網站中的環境。

   [![Azure 時間序列深入解析環境選取](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. 接下來，會顯示在所選時間範圍內顯示所有事件計數的圖表。 這裡您有許多控制項：

    - **字詞編輯器面板**：字詞空間是您用來查詢環境的位置。 它位於畫面左邊：
      - **MEASURE**：這個下拉式清單會顯示所有數值資料行 (**雙精度**) 。
      - **分割依據**：這個下拉式清單會顯示)  (**字串** 的類別資料行。
      - 您可以啟用步驟插補、顯示最小值和最大值，以及從 [ **量值**] 旁的控制台調整 y 軸。 您也可以調整所顯示的資料是否為數據的計數、平均或總和。
      - 您最多可以加入五個詞彙，以在相同的 X 軸上查看。 選取 [ **新增** ] 以新增新的字詞，或使用 [ **複製此詞彙** ] 按鈕來加入現有字詞的複本。

        [![詞彙選取、篩選和查詢面板](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - 述**詞：使用**述詞來快速篩選您的事件，方法是使用下表所列的一組運算元。 如果您選取或按一下來進行搜尋，述詞會根據該搜尋自動進行更新。 支援的運算元類型包括：

         |作業  |支援的類型  |備註  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**、 **DateTime**、 **TimeSpan**       |         |
         |**=**, **!=**, **<>**     | **String**、 **Bool**、 **Double**、 **DateTime**、 **TimeSpan**、 **Null**        |         |
         |**IN**     | **String**、 **Bool**、 **Double**、 **DateTime**、 **TimeSpan**、 **Null**        |  所有的運算元都應該是相同類型或為 **Null** 常數。        |
         |**HAS**     | **String**        |  右邊只允許常數位串常值。 不允許空字串和 **Null** 。       |

      - **查詢範例**

         [![範例 Gen1 查詢](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. 您可以使用 [ **間隔大小** ] 滑杆工具來放大和縮小相同 timespan 的間隔。 滑杆可讓您更精確地控制多個時間配量之間的移動，將配量顯示為小到毫秒的平滑趨勢，讓您能夠顯示及分析資料的細微、高解析度的剪下。 滑杆的預設起始點會設定為您的選取專案中最理想的資料檢視，以平衡解析度、查詢速度和資料細微性。

1. **時間筆刷**工具可讓您輕鬆地從一個時間範圍流覽至另一個 timespan。

1. 選取 [ **儲存** ] 圖示以儲存目前的查詢，並與其他環境使用者共用。 當您選取 [ **開啟** ] 圖示時，可以在您可以存取的環境中，檢查所有已儲存的查詢和其他使用者的任何共用查詢。

   [![查詢](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>顯現資料

1. 使用「 **透視圖視圖** 」工具可同時查看最多四個唯一的查詢。 [ **透視圖視圖** ] 按鈕位於圖表的右上角。

   [![選取要加入至 [透視圖] 窗格的查詢](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. 查看圖表以視覺化方式流覽您的資料，並使用 **圖表** 工具：

    - **選取** 或 **按一下** 特定的 timespan 或單一資料數列。
    - 在時間範圍選取範圍內，您可以縮放或探索事件。
    - 在資料序列內，您可以由另一個資料行分割序列、將序列新增為新的字詞、只顯示選取的序列、排除選取的序列、釘選該序列或從選取的序列探索事件。
    - 在圖表左側的篩選區域中，您可以查看所有顯示的資料序列，然後依值或名稱重新排序。 您也可以查看所有資料數列或任何已釘選或取消固定的數列。 您可以選取單一資料數列，然後依另一個資料行分割序列、將數列新增為新的字詞、只顯示選取的序列、排除選取的序列、釘選該數列，或從選取的數列探索事件。
    - 當您同時查看多個字詞時，可以堆疊、取消堆疊、查看有關資料數列的其他資料，並在所有詞彙中使用相同的 y 軸。 使用圖表右上角的按鈕。

    [![圖表工具右上角選項設定](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. 使用 **熱度圖** 快速找出指定查詢中的唯一或異常資料數列。 只有一個搜尋字詞可視覺化為熱度圖。

    [![時間序列深入解析瀏覽器熱度圖圖表 ](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. 當您選取或以滑鼠右鍵按一下來探索事件時，[ **事件** ] 面板就會成為可用。 您可以在這裡查看所有原始事件，並將事件匯出為 JSON 或 CSV 檔案。 Azure 時間序列深入解析儲存所有原始資料。

    [![事件](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. 探索事件之後，請選取 [ **統計** 資料] 索引標籤，以公開模式和資料行統計資料。

    - **模式**：這項功能會主動呈現所選資料區域中最統計的重大模式。 您不需要查看數以千計的事件，就能瞭解哪些模式需要最多時間和能源。 有了 Azure 時間序列深入解析，您可以直接跳到這些統計的重大模式，以繼續進行分析。 這項功能對於事後調查歷史資料也很有幫助。
    - 資料**行統計**資料：資料行統計資料會提供圖表及資料表，將所選資料數列之每個資料行的資料細分為選取的 timespan。

      [![統計資料行圖表和選項](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

現在您已瞭解 Azure 時間序列深入解析 explorer web 應用程式中可用的主要功能、設定設定和顯示選項。

## <a name="next-steps"></a>接下來的步驟

- 瞭解如何 [診斷和解決](time-series-insights-diagnose-and-solve-problems.md) Azure 時間序列深入解析環境中的問題。

- 引導 [Azure 時間序列深入解析快速入門](time-series-quickstart.md) 導覽。
