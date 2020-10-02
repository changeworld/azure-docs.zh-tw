---
title: 將您的環境連線到 Power BI-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解如何將 Azure 時間序列深入解析連接到 Power BI，以在整個組織中共用、圖表及顯示資料。
author: deepakpalled
ms.author: dpalled
manager: diviso
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 105faf796583eb0f272eea7a316648993fdafd0d
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653769"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>從 Power BI 中的 Azure 時間序列深入解析將資料視覺化

Azure 時間序列深入解析是在雲端儲存、管理、查詢和視覺化時間序列資料的平臺。 [Power BI](https://powerbi.microsoft.com) 是具有豐富視覺效果功能的商務分析工具，可讓您在整個組織中共用見解和結果。 這兩項服務現在都可以整合，以獲得 Azure 時間序列深入解析的固有視覺效果功能以及 Power BI 的最佳效果。

您將學習如何：

* 使用雲端連接器將 Azure 時間序列深入解析連接到 Power BI
* 在 Power BI 中使用您的資料建立視覺效果
* 將報表發行至 Power BI，並與您的組織其餘部分共用

最後，您將瞭解如何透過 Azure 時間序列深入解析將時間序列資料視覺化，並使用強大的資料視覺效果和 Power BI 的簡易共用功能來增強資料。

如果您還沒有訂用帳戶，請務必註冊 [免費的 Azure 訂](https://azure.microsoft.com/free/) 用帳戶。

## <a name="prerequisites"></a>Prerequisites

* 下載並安裝最新版本的 [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* 擁有或建立 [Azure 時間序列深入解析 Gen2 環境](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
>
> * 目前只有在使用 **暖存放區**設定的 Azure 時間序列深入解析 Gen2 環境中支援連接器。
> * 如果您有從另一個 Azure AD 租使用者對 Azure 時間序列深入解析 Gen2 環境的來賓存取權，您將無法存取連接器。 瞭解 [環境存取原則](./concepts-access-policies.md)。

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>將資料從 Azure 時間序列深入解析連線到 Power BI

若要將 Azure 時間序列深入解析環境連線到 Power BI，請遵循下列步驟：

1. 開啟 Azure 時間序列深入解析 Explorer
1. 將資料匯出為查詢或原始資料
1. 開啟 Power BI Desktop
1. 從自訂查詢載入

### <a name="export-data-into-power-bi-desktop"></a>將資料匯出至 Power BI desktop

開始進行之前：

1. 開啟 Azure 時間序列深入解析 Explorer 並策展您的資料。
1. 一旦您建立了您滿意的視圖，請流覽至 [ **其他動作** ] 下拉式功能表，然後選取 **[連接到 Power BI]**。

    [![Azure 時間序列深入解析 Explorer 匯出](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. 在此索引標籤中設定您的參數：

   1. 指定要查看的相對時間範圍。 如果您對現有的觀點感到滿意，請將其保留為 **現有的時間範圍**。

   1. 在 **匯總** 和 **原始事件**之間進行選擇。

       > [!NOTE]
       > 您稍後可以在 Power BI 中匯總資料，但在匯總之後，您就無法還原成原始資料。

       > [!NOTE]
       > 原始事件層級資料有250000的事件計數限制。

       [![連接](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. 如果您尚未設定具有 **暖存放區**的 Azure 時間序列深入解析環境，您將會收到警告。

       [![暖存放區警告](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > 您可以在 Azure 入口網站中設定適用于 **暖存放區** 的現有實例。

1. 選取 [ **將查詢複製到剪貼**簿]。
1. 現在啟動 Power BI Desktop。
1. 在 [ **首頁** ] 索引標籤的 [Power BI Desktop] 中，選取左上角的 [ **取得資料** ]，然後選取 [ **其他**]。

    [![首頁下拉式清單](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. 搜尋 **Azure 時間序列深入解析**，選取 **Azure 時間序列深入解析 (Beta) **，然後 **連接**。

    [![將 Power BI 連接到 Azure 時間序列深入解析](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    或者，流覽至 [ **Azure** ] 索引標籤，選取 **Azure 時間序列深入解析 (Beta) **，然後 **連接**。

1. [訊息] 對話方塊會顯示要求許可權以連線至協力廠商資源。 選取 [ **繼續**]。

    [![選擇 [建立自訂查詢]](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. 在 [ **資料來源**] 底下的下拉式功能表中，選擇 [ **建立自訂查詢**]。 從剪貼簿貼上選擇性的 **自訂查詢， (** 下方的選擇性) 欄位，然後按 **[確定]**。

    [![傳入自訂查詢並選取 [確定]](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. 資料表現在將會載入。 按下 **載入** 以載入 Power BI。

    [![檢查資料表中載入的資料，然後選取 [載入]](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

如果您已完成這些步驟，請直接跳到下一節。

## <a name="create-a-report-with-visuals"></a>使用視覺效果來建立報表

既然您已將資料匯入 Power BI，現在就可以建立具有視覺效果的報表。

1. 在視窗的左側，確定您已選取 **報表** 視圖。

    [![螢幕擷取畫面：顯示報表檢視圖示。](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. 在 [ **視覺效果** ] 資料行中，選取您選擇的視覺效果。 例如，選取 [ **折線圖**]。 這會將空白折線圖新增至畫布。

1. 在 [ **欄位** ] 清單中選取 **_Timestamp** ，然後將它拖曳到 [ **軸** ] 欄位，以顯示 X 軸上的專案。 請務必切換至 **_Timestamp** ，作為 **軸** 的值 (預設為 **日期** 階層) 。

    [![螢幕擷取畫面顯示已選取 _Timestamp 的時間戳記功能表。](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1. 同樣地，在 [ **欄位** ] 清單中選取 [ **TimeSeriesId** ]，並將它拖曳至 [ **值** ] 欄位，以顯示 Y 軸上的專案。

    [![建立折線圖](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. 若要將另一個圖表新增至畫布，請選取折線圖外面的畫布上任何位置，然後重複此程式。

    [![建立要共用的其他圖表](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

建立報表之後，您可以將它發佈至 Power BI Reporting Services。

## <a name="advanced-editing"></a>Advanced 編輯

如果您已經在 Power BI 中載入資料集，但想要修改查詢 (例如) 的日期/時間或環境識別碼參數，您可以透過 Power BI 的進階編輯器功能來完成這項作業。 若要深入瞭解，請參閱 [Power BI 檔](https://docs.microsoft.com/power-bi/desktop-query-overview) 。

概述：

1. 在 Power BI Desktop 中，選取 [ **編輯查詢**]。
1. 按 **進階編輯器**。

    [![在進階編輯器中編輯查詢](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. 視需要修改 JSON 承載。
1. 選取 [**完成**]，然後關閉 [ **Power Query 編輯器] 視窗**內 **&** 套用]。

介面現在會反映您所套用的所需變更。  

## <a name="next-steps"></a>後續步驟

* 請參閱 Azure 時間序列深入解析 [Power BI 連接器的概念](https://docs.microsoft.com/power-bi/desktop-query-overview) 。

* 深入瞭解 [Power BI desktop](https://docs.microsoft.com/power-bi/desktop-query-overview)。
