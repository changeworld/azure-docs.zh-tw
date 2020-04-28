---
title: 將您的環境連接到 Power BI-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解如何將 Azure 時間序列深入解析連接到 Power BI，以在整個組織中共用、繪製圖表和顯示資料。
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75863837"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>在 Power BI 中從時間序列深入解析將資料視覺化

Azure 時間序列深入解析是在雲端中儲存、管理、查詢和視覺化時間序列資料的平臺。 [Power BI](https://powerbi.microsoft.com)是具有豐富視覺效果功能的商務分析工具，可讓您在整個組織內共用見解和結果。 這兩項服務現在都可以整合，以獲得時間序列深入解析的固有視覺效果功能，以及 Power BI 的最佳做法。

您將學習如何：

* 使用雲端連接器將時間序列深入解析連線至 Power BI
* 在 Power BI 中使用您的資料建立視覺效果
* 將報表發行至 Power BI，並與貴組織的其他人共用

最後，您將瞭解如何透過 Azure 時間序列深入解析來視覺化時間序列資料，並利用強大的資料視覺效果和 Power BI 的輕鬆共用功能來增強它。

如果您還沒有訂用帳戶，請務必註冊[免費的 Azure 訂](https://azure.microsoft.com/free/)用帳戶。

## <a name="prerequisites"></a>Prerequisites

* 下載並安裝最新版本的[Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* 建立或建立[Azure 時間序列深入解析 Preview 實例](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> 在針對**暖存放區**設定的時間序列深入解析 Preview*隨用隨付*環境中，目前支援 Power BI 連接器。

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>將資料從時間序列深入解析連線到 Power BI

若要將您的時間序列深入解析環境連接到 Power BI，請遵循下列步驟：

1. 開啟時間序列深入解析總管
1. 將資料匯出為查詢或原始資料
1. 開啟 Power BI Desktop
1. 從自訂查詢載入

### <a name="export-data-into-power-bi-desktop"></a>將資料匯出至 Power BI desktop

開始進行之前：

1. 開啟時間序列深入解析 Preview Explorer，並策展您的資料。
1. 一旦您建立了您滿意的視圖，請流覽至 [**其他動作**] 下拉式功能表，然後選取 **[連接到 Power BI]**。

    [![時間序列深入解析預覽瀏覽器匯出](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. 在此索引標籤中設定您的參數：

   1. 指定要查看的相對時間範圍。 如果您對現有的觀點感到滿意，請將其保留為**現有的時間範圍**。
   
   1. 在**匯總**和**原始事件**之間選擇。 
   
       > [!NOTE]
       > 您稍後可以在 Power BI 中匯總資料，但無法在匯總之後還原為原始資料。 
       
       > [!NOTE]
       > 原始事件層級資料的事件計數限制為 100-K。

       [![連線](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. 如果您尚未為**暖存放區**設定時間序列深入解析實例，您將會收到警告。

       [![暖存放區警告](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > 您可以在 Azure 入口網站中，為**暖存放區**設定現有的實例。

1. 選取 [**將查詢複製到剪貼**簿]。
1. 現在，啟動 Power BI Desktop。
1. 在 [**首頁**] 索引標籤的 Power BI Desktop 中，選取左上角的 [**取得資料**]，然後選取 [**更多**]。

    [![首頁下拉式清單](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. 搜尋**時間序列深入解析**，選取 **[Azure 時間序列深入解析（搶鮮版（Beta））**]，然後 **[連線]**。

    [![將 Power BI 連接到時間序列深入解析](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    或者，流覽至 [ **Azure** ] 索引標籤，選取 [ **Azure 時間序列深入解析（Beta）**]，然後按一下 **[連線]**。
    
1. [訊息] 對話方塊隨即顯示，要求您提供連線到協力廠商資源的許可權。 選取 [**繼續**]。

    [![選擇 [建立自訂查詢]](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. 在 [**資料來源**] 底下的下拉式功能表中，選擇 [**建立自訂查詢**]。 從剪貼簿貼到下方的選擇性 [**自訂查詢（選擇性）** ] 欄位中，然後按 **[確定]**。

    [![傳入自訂查詢，然後選取 [確定]](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. 資料表現在將會載入。 按下 [**載入**] 以載入 Power BI。

    [![檢查資料表中載入的資料，然後選取 [載入]](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

如果您已完成這些步驟，請跳到下一節。

## <a name="create-a-report-with-visuals"></a>使用視覺效果來建立報表

既然您已將資料匯入 Power BI，現在可以建立具有視覺效果的報表。

1. 請確定您已選取 [**報表**] 視圖，然後在視窗左側。

    [![選取報表檢視](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  在 [視覺**效果**] 資料行中，選取您選擇的視覺效果。 例如，選取 [**折線圖**]。 這會在您的畫布上加入一個空白折線圖。
 
1.  在 [**欄位**] 清單中選取 **[時間戳記]** ，並將它拖曳至 [**軸**] 欄位，以沿著 X 軸顯示專案。

1.  同樣地，在 [**欄位**] 清單中，選取 [ **TimeSeriesId** ]，並將它拖曳至 [**值**] 欄位，以沿著 Y 軸顯示專案。

    [![建立折線圖](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  若要將另一個圖表新增至畫布，請選取折線圖外畫布上的任何位置，然後重複此程式。

    [![建立其他要共用的圖表](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

建立報表之後，您可以將它發行至 Power BI Reporting Services。

## <a name="advanced-editing"></a>先進的編輯

如果您已經在 Power BI 中載入資料集，但想要修改查詢（例如日期/時間或環境識別碼參數），您可以透過 Power BI 的進階編輯器功能來執行這項操作。 若要深入瞭解，請參閱[Power BI 檔](https://docs.microsoft.com/power-bi/desktop-query-overview)。

概述：

1. 在 Power BI Desktop 中，選取 [**編輯查詢**]。
1. 按**進階編輯器**。

    [![編輯進階編輯器中的查詢](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. 視需要修改 JSON 承載。
1. 選取 [**完成**]，然後**關閉 &** 在 [ **Power Query 編輯器] 視窗**內套用。

介面現在會反映您所套用的所需變更。  

## <a name="next-steps"></a>後續步驟

* 閱讀 Azure 時間序列深入解析[Power BI 連接器概念](https://docs.microsoft.com/power-bi/desktop-query-overview)的相關資訊。

* 深入瞭解[Power BI desktop](https://docs.microsoft.com/power-bi/desktop-query-overview)。

* 閱讀[時間序列深入解析 GA explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)和[時間序列深入解析預覽瀏覽器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)。
