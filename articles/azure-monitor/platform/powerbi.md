---
title: 將 Azure Log Analytics 資料匯入至 Power BI | Microsoft Docs
description: Power BI 是 Microsoft 的雲端架構商務分析服務，能提供豐富的視覺效果和不同資料集的分析報告。  本文說明如何設定將 Log Analytics 資料匯入 Power BI，並將它設定為自動重新整理。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 8b9624313716081a039b12c73e54b9f7afc2a323
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505307"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>將 Azure 監視器記錄資料匯入 Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)是 Microsoft 的雲端式商務分析服務，可提供豐富的視覺效果和報表來分析不同的資料集。  您可以將 Azure 監視器記錄查詢的結果匯入 Power BI 資料集，以便利用其功能，例如合併不同來源的資料，以及共用 web 和行動裝置上的報表。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>概觀
若要從 Azure 監視器中的[Log Analytics 工作區](manage-access.md)將資料匯入 Power BI，您可以根據 Azure 監視器中的[記錄查詢](../log-query/log-query-overview.md)，在 Power BI 中建立資料集。  每次資料集重新整理時，就會執行查詢。  然後，您即可使用資料集中的資料建立 Power BI 報表。  若要在 Power BI 中建立資料集，您必須從 Log Analytics 將您的查詢匯出到 [Power Query (M) 語言](/powerquery-m/power-query-m-language-specification)。  接著可以用它在 Power BI Desktop 中建立查詢，然後將它發佈至 Power BI 做為資料集。  以下將詳細說明此流程。

![Log Analytics 到 Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>匯出查詢
首先建立[記錄查詢](../log-query/log-query-overview.md)，以傳回您要填入 Power BI 資料集的資料。  您接著可以將該查詢匯出到可以由 Power BI Desktop 使用的 [Power Query (M) 語言](/powerquery-m/power-query-m-language-specification)。

1. [在 Log Analytics 中建立記錄查詢](../log-query/get-started-portal.md)，以解壓縮資料集的資料。
2. 選取**Export**  >  **[匯出 Power BI 查詢（M）**]。  這會將查詢匯出至名為**PowerBIQuery.txt**的文字檔。 

    ![匯出記錄搜尋](media/powerbi/export-analytics.png)

3. 開啟該文字檔，然後複製其中的內容。

## <a name="import-query-into-power-bi-desktop"></a>將查詢匯入至 Power BI Desktop
Power BI Desktop 是桌面應用程式，可讓您建立可發行至 Power BI 的資料集和報表。  您也可以使用它，利用從 Azure 監視器匯出的 Power Query 語言來建立查詢。 

1. 請安裝 [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (如果尚未安裝)，然後開啟該應用程式。
2. 選取 [**取得資料**] [  >  **空白查詢**] 以開啟新的查詢。  然後選取**進階編輯器**，並在查詢中貼上匯出的檔案內容。 按一下 [完成] 。

    ![Power BI Desktop 查詢](media/powerbi/desktop-new-query.png)

5. 執行查詢並顯示其結果。  可能會提示您提供連接到 Azure 的認證。  
6. 輸入查詢的描述性名稱。  預設值是 **Query1**。 按一下**關閉並套用**，將資料集新增至報表。

    ![Power BI Desktop 名稱](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>發行至 Power BI
您發行至 Power BI 時，會建立資料集和報表。  如果您在 Power BI Desktop 中建立報表，將連同您的資料一起發佈。  如果未建立，則將建立空白報表。  您可以在 Power BI 中修改該報表，也建立依照資料集建立新的報表。

1. 依照資料建立報表。  如果您不熟悉如何進行，請參閱 [Power BI Desktop 文件](/power-bi/desktop-report-view)。  
1. 您準備將它傳送至 Power BI 時時，請按一下**發行**。  
1. 出現提示時，請在 Power BI 帳戶中選取目的地。  除非您有特定的目的地，否則請使用**我的工作區**。

    ![Power BI Desktop 發佈](media/powerbi/desktop-publish.png)

1. 發佈完成時，按一下**在 Power BI 中開啟**，以新的資料集開啟 Power BI。


### <a name="configure-scheduled-refresh"></a>設定排程的重新整理
在 Power BI 中建立的資料集將具有與您先前在 Power BI Desktop 看見的相同資料。  您需要定期重新整理資料集以再次執行查詢，並將最新的資料填入 Azure 監視器。  

1. 按一下您上傳報表的工作區，並選取**資料集**功能表。 
1. 選取新的資料集旁邊的快顯功能表，並選取**設定**。 
1. 在**資料來源認證**下，應該會出現認證無效的訊息。  這是因為您尚未提供資料集重新整理其資料時使用的認證。  
1. 按一下 [**編輯認證**]，並指定可存取 Azure 監視器中 Log Analytics 工作區的認證。 如果您需要雙因素驗證，請選取 [ **OAuth2** ]，以提示**驗證方法**以使用您的認證登入。

    ![Power BI 排程](media/powerbi/powerbi-schedule.png)

5. 在**排定的重新整理**選項下，開啟**維持資料的最新狀態**。  您可以選擇變更**重新整理頻率**，以及執行重新整理的一個或多個特定時間。

    ![Power BI 重新整理](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>後續步驟
* 深入了解 [記錄檔搜尋](../log-query/log-query-overview.md) ，建置可以匯出至 Power BI 的查詢。
* 深入瞭解[Power BI](https://powerbi.microsoft.com) ，以根據 Azure 監視器記錄匯出來建立視覺效果。
