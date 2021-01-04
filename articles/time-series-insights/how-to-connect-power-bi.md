---
title: 將您的環境連線到 Power BI-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解如何將 Azure 時間序列深入解析連接到 Power BI，以在整個組織中共用、圖表及顯示資料。
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 07e79dbde142400677901ee02903144f9a42cd6b
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740706"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>從 Power BI 中的 Azure 時間序列深入解析將資料視覺化

Azure 時間序列深入解析是在雲端儲存、管理、查詢和視覺化時間序列資料的平臺。 [Power BI](https://powerbi.microsoft.com) 是具有豐富視覺效果功能的商務分析工具，可讓您在整個組織中共用見解和結果。 這兩項服務現在已整合，可讓您使用強大的資料視覺效果和 Power BI 的簡易共用功能，增強 Azure 時間序列深入解析的強大分析。

您將學習如何：

* 使用原生 Azure 時間序列深入解析連接器將 Azure 時間序列深入解析連接到 Power BI
* 在 Power BI 中使用您的時間序列資料建立視覺效果
* 將報表發行至 Power BI，並與您的組織其餘部分共用


## <a name="prerequisites"></a>必要條件

* 如果您沒有帳戶，請註冊[免費 Azure 訂用帳戶](https://azure.microsoft.com/free/)。
* 下載並安裝最新版本的 [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* 擁有或建立 [Azure 時間序列深入解析 Gen2 環境](./how-to-provision-manage.md)

請檢查 [環境存取原則](./concepts-access-policies.md) ，並確定您可以直接存取或來賓存取 Azure 時間序列深入解析 Gen2 環境。 

> [!IMPORTANT]
> * 下載並安裝 [Power BI Desktop](https://powerbi.microsoft.com/downloads/)的最新版本。 若要遵循本文中的步驟進行，請確定您至少已安裝2020年12月的 (2.88.321.0) 版本的 Power BI Desktop。 

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>將資料從 Azure 時間序列深入解析連線到 Power BI

### <a name="1-export-data-into-power-bi-desktop"></a>1. 將資料匯出至 Power BI desktop

開始進行之前：

1. 開啟 Azure 時間序列深入解析 Gen2 Explorer，並策展您的資料。 這是將匯出到 Power BI 中的資料。
1. 一旦您建立了您滿意的視圖，請流覽至 [ **其他動作** ] 下拉式功能表，然後選取 **[連接到 Power BI]**。

    [![Azure 時間序列深入解析 Gen2 Explorer 匯出](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. 設定匯出參數：

   * **資料格式**：選擇您要將 **匯總資料** 或 **原始事件** 匯出至 Power BI。 

       > [!NOTE]
       > * 如果您匯出原始事件，您稍後可以在 Power BI 中匯總該資料。 但是，如果您匯出匯總資料，您就無法在 Power BI 中還原成原始資料。 
       > * 原始事件層級資料有250000的事件計數限制。

   * **時間範圍**：選擇您想要查看 Power BI 中的 **固定** 時間範圍或 **最新** 資料。 選擇固定時間範圍即表示您已製作圖表的搜尋範圍中的資料會匯出至 Power BI。 選擇最新的時間範圍，表示 Power BI 將會抓取您所選擇之搜尋範圍的最新資料 (例如，如果您將任何1小時的資料繪製成圖表，然後選擇 [最新] 設定，Power BI 連接器一律會針對最新的1小時資料進行查詢 ) 
  
   * **商店類型**：選擇是否要針對 **暖存放區** 或 **冷存放區** 執行選取的查詢。 

    > [!TIP]
    > * Azure 時間序列深入解析 Explorer 會根據您選擇匯出的資料，自動選取建議的參數。 

1. 設定好設定之後，請選取 [ **將查詢複製到剪貼** 簿]。

    [![Azure 時間序列深入解析 Explorer 匯出強制回應](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

2. 啟動 Power BI Desktop。
   
3. 在 [ **首頁** ] 索引標籤的 [Power BI Desktop] 中，選取左上角的 [ **取得資料** ]，然後選取 [ **其他**]。

    [![在 Power BI 中取得資料](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

4. 搜尋 **Azure 時間序列深入解析**，選取 **Azure 時間序列深入解析 (Beta)**，然後 **連接**。

    [![將 Power BI 連接到 Azure 時間序列深入解析](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    或者，流覽至 [ **Azure** ] 索引標籤，選取 **Azure 時間序列深入解析 (Beta)**，然後 **連接**。

5. 將您從 Azure 時間序列深入解析 Explorer 複製的查詢貼到 [ **自訂查詢** ] 欄位中，然後按 **[確定]**。

    [![貼上自訂查詢，然後選取 [確定]](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

6.  資料表現在將會載入。 按下 **載入** 以載入 Power BI。 如果您想要對資料進行任何轉換，您現在可以按一下 [ **轉換資料**] 來進行轉換。 您也可以在資料載入後進行轉換。

    [![檢查資料表中的資料，然後選取 [載入]](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>使用視覺效果來建立報表

既然您已將資料匯入 Power BI，現在就可以建立具有視覺效果的報表。

1. 在視窗的左側，確定您已選取 **報表** 視圖。

    [![螢幕擷取畫面：顯示報表檢視圖示。](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. 在 [ **視覺效果** ] 資料行中，選取您選擇的視覺效果。 例如，選取 [ **折線圖**]。 這會將空白折線圖新增至畫布。

1.  在 [ **欄位** ] 清單中選取 **_Timestamp** ，然後將它拖曳到 [ **軸** ] 欄位，以沿著 X 軸顯示時間。 請務必切換至 **_Timestamp** ，作為 **軸** 的值 (預設為 **日期** 階層) 。

    [![選取 _Timestamp](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  同樣地，在 [ **欄位** ] 清單中選取您想要繪製的變數，並將它拖曳至 [ **值** ] 欄位，以顯示 Y 軸的值。 選取您的時間序列識別碼值，並將它拖曳至 [ **圖例** ] 欄位，以在圖表中建立多行，每個時間序列識別碼一個。 這會呈現類似 Azure 時間序列深入解析 Explorer 所提供的觀點！ 

    [![建立基本折線圖](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. 若要將另一個圖表新增至畫布，請選取折線圖外面的畫布上任何位置，然後重複此程式。

    [![建立要共用的其他圖表](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

建立報表之後，您可以將其發佈至 Power BI Reporting Services，並與組織中的其他人共用。

## <a name="advanced-editing"></a>Advanced 編輯
如果您已經在 Power BI 中載入資料集，但想要修改查詢 (例如) 的日期/時間或環境識別碼參數，您可以透過 Power BI 的進階編輯器功能來完成這項作業。 請參閱 [Power BI 檔](https://docs.microsoft.com/power-bi/desktop-query-overview) ，以深入瞭解如何使用 **Power Query 編輯器** 進行變更。 

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Power BI desktop](https://docs.microsoft.com/power-bi/desktop-query-overview)。

* 深入瞭解如何查詢 Azure 時間序列深入解析 Gen2 中的 [資料](concepts-query-overview.md) 。
