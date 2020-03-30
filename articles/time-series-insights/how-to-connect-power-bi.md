---
title: 將環境連接到 Power BI - Azure 時間序列見解 |微軟文檔
description: 瞭解如何將 Azure 時間序列見解連接到 Power BI 以在整個組織中共用、繪製圖表和顯示資料。
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863837"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>在 Power BI 中視覺化來自時間序列洞察的資料

Azure 時間序列見解是一個用於在雲中存儲、管理、查詢和視覺化時間序列資料的平臺。 [Power BI](https://powerbi.microsoft.com)是一個業務分析工具，具有豐富的視覺化功能，允許您在整個組織中共用見解和結果。 現在，可以集成這兩種服務，以充分利用時序見解的固有視覺化功能以及 Power BI 的功能。

您將學習如何：

* 使用雲連接器將時間序列見解連接到 Power BI
* 使用 Power BI 中的資料創建視覺物件
* 將報表發佈到 Power BI 並與組織的其餘部分共用

最後，您將學習如何通過 Azure 時間序列見解視覺化時間序列資料，並通過 Power BI 的強大資料視覺化和輕鬆共用功能增強資料。

如果還沒有[免費 Azure 訂閱](https://azure.microsoft.com/free/)，請確保註冊。

## <a name="prerequisites"></a>Prerequisites

* 下載並安裝最新版本的[Power BI 桌面](https://powerbi.microsoft.com/downloads/)
* 擁有或創建[Azure 時間序列見解預覽實例](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> 當前支援為**暖存儲**配置的時序見解預覽即*用即付*環境中支援 Power BI 連接器。

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>將時間序列洞察的資料連線到 Power BI

要將時序見解環境連接到 Power BI，請按照以下步驟操作：

1. 打開時間序列見解資源管理器
1. 將資料匯出為查詢或原始資料
1. 打開電源 BI 桌面
1. 從自訂查詢載入

### <a name="export-data-into-power-bi-desktop"></a>將資料匯出到 Power BI 桌面

開始進行之前：

1. 打開時間序列見解預覽資源管理器並整理資料。
1. 創建您滿意的視圖後，導航到 **"更多操作**"下拉式功能表，然後選擇"**連接到電源 BI**"。

    [![時間序列見解預覽資源管理器匯出](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. 在此選項卡內設置參數：

   1. 指定要查看的相對時間範圍。 如果您對現有視圖感到滿意，請保留此視圖為**現有時間幀**。
   
   1. 在**聚合**事件和**原始事件**之間進行選擇。 
   
       > [!NOTE]
       > 您始終可以在 Power BI 中稍後聚合資料，但在聚合後無法還原為原始資料。 
       
       > [!NOTE]
       > 原始事件級別資料有 100-K 事件計數限制。

       [![連接](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. 如果您尚未為**暖存儲**配置時序見解實例，您將收到一條警告。

       [![暖存儲警告](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > 您可以在 Azure 門戶中為**暖存儲**配置現有實例。

1. 選擇 **"將查詢複製到剪貼簿**"。
1. 現在，啟動 Power BI 桌面。
1. 在 **"主頁**"選項卡上的"Power BI 桌面"中，選擇左上角**的"獲取資料**"，然後**選擇"更多**"。

    [![家庭下拉清單](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. 搜索**時間序列見解**，選擇**Azure 時間序列見解 （Beta），** 然後**連接**。

    [![將 POWER BI 連接到時間序列見解](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    或者，導航到**Azure**選項卡，選擇**Azure 時間序列見解 （Beta），** 然後**連接**。
    
1. 將顯示一個消息對話方塊，請求獲得連接到協力廠商資源的許可權。 選擇 **"繼續**"。

    [![選擇"創建自訂查詢"](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. 在**資料來源**下的下拉式功能表中，選擇 **"創建自訂查詢**"。 從剪貼簿粘貼到下面的可選**自訂查詢（可選）** 欄位，然後按 **"確定**"。

    [![在自訂查詢中傳遞並選擇"確定"](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. 資料表現在將載入。 按 **"負載"** 以載入到電源 BI 中。

    [![查看表中載入的資料並選擇"載入"](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

如果已完成這些步驟，請跳到下一節。

## <a name="create-a-report-with-visuals"></a>使用視覺效果來建立報表

現在，您已將資料導入 Power BI，是時候使用視覺物件生成報表了。

1. 在視窗的左側，請確保選擇了 **"報表"** 視圖。

    [![選擇報表檢視](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  在 **"視覺化"** 列中，選擇您選擇的視覺物件。 例如，選擇**折線圖**。 這將向畫布添加空白折線圖。
 
1.  在 **"欄位"** 清單中，選擇**時間戳記**並將其拖動到 **"軸"** 欄位以沿 X 軸顯示專案。

1.  同樣，在 **"欄位"** 清單中，選擇**TimeSeriesId**並將其拖動到 **"值"** 欄位以沿 Y 軸顯示專案。

    [![建立折線圖](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  要向畫布添加另一個圖表，請選擇折線圖外的畫布上的任意位置，然後重複此過程。

    [![創建要共用的其他圖表](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

創建報表後，可以將其發佈到 Power BI 報表服務。

## <a name="advanced-editing"></a>高級編輯

如果已在 Power BI 中載入了資料集，但希望修改查詢（如日期/時間或環境 ID 參數），則可以通過 Power BI 的高級編輯器功能執行此操作。 請參閱[Power BI 文檔](https://docs.microsoft.com/power-bi/desktop-query-overview)以瞭解更多資訊。

概述：

1. 在"電源 BI 桌面"中，選擇 **"編輯查詢**"。
1. 按**高級編輯器**。

    [![編輯高級編輯器中的查詢](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. 根據需要修改 JSON 有效負載。
1. 選擇 **"完成"，** 然後在 **"電源查詢編輯器"視窗中****&關閉應用**"。

該介面現在將反映您應用的所需更改。  

## <a name="next-steps"></a>後續步驟

* 閱讀有關 Azure 時間序列見解的[Power BI 連接器概念](https://docs.microsoft.com/power-bi/desktop-query-overview)。

* 瞭解有關[Power BI 桌面](https://docs.microsoft.com/power-bi/desktop-query-overview)的更多。

* 閱讀[時間序列見解 GA 資源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)和[時間序列見解預覽資源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)。
