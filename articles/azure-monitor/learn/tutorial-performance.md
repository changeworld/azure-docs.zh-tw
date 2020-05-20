---
title: 使用 Azure Application Insights 診斷效能問題 | Microsoft Docs
description: 使用 Azure Application Insights 來尋找並診斷應用程式效能問題的教學課程。
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/13/2019
ms.custom: mvc
ms.openlocfilehash: 98d7c1552a7b1f2b02ae4df1cad24e20f7ac76e1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223675"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>使用 Azure Application Insights 尋找並診斷效能問題

Azure Application Insights 會從您的應用程式收集遙測，以協助分析其作業和效能。  使用此資訊可識別可能發生的問題，或識別對使用者影響最大的應用程式增強功能。  本教學課程會引導您完成以下程序：分析應用程式的伺服器元件效能，以及從用戶端角度分析效能。  您會了解如何：

> [!div class="checklist"]
> * 識別伺服器端作業的效能
> * 分析伺服器作業，以判斷效能緩慢的根本原因
> * 識別最慢的用戶端作業
> * 使用查詢語言分析頁面檢視的詳細資料


## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

- 使用下列工作負載安裝 [Visual Studio 2019](https://www.visualstudio.com/downloads/)：
    - ASP.NET 和 Web 開發
    - Azure 開發
- 將 .NET 應用程式部署至 Azure，並[啟用 Application Insights SDK](../../azure-monitor/app/asp-net.md)。
- [啟用應用程式的 Application Insights Profiler](../../azure-monitor/app/profiler.md#installation)。

## <a name="log-in-to-azure"></a>登入 Azure
在 [https://portal.azure.com](https://portal.azure.com) 上登入 Azure 入口網站。

## <a name="identify-slow-server-operations"></a>識別緩慢的伺服器作業
Application Insights 會收集您應用程式中不同作業的效能詳細資料。 藉由識別持續時間最長的作業，您可以診斷潛在的問題，或為進行中的開發設定最適合的目標，以改善應用程式的整體效能。

1. 選取 [Application Insights]  ，然後選取您的訂用帳戶。  
1. 若要開啟 [效能] 面板，選取 [調查] 功能表下方的 [效能]，或按一下 [伺服器回應時間] 圖形。

    ![效能](media/tutorial-performance/1-overview.png)

2. [效能]  面板會顯示應用程式每個作業的計數和平均持續時間。  您可以使用這項資訊來識別對使用者影響最大的作業。 在此範例中，**GET Customers/Details** 和 **GET Home/Index** 是可能的調查候選項目，因其持續時間和呼叫數目相對較高。  其他作業的持續時間可能更高，但因呼叫次數不頻繁，改善後的影響不大。  

    ![效能伺服器面板](media/tutorial-performance/2-server-operations.png)

3. 此圖形目前顯示選取的作業在一段時間內的平均持續時間。 您可以切換到第 95 個百分位數，以找出效能問題。 將您感興趣的作業釘選至圖形以新增它們。  這裡顯示有些尖峰值得調查。  減少此圖形的時間範圍，以進一步隔離它。

    ![釘選作業](media/tutorial-performance/3-server-operations-95th.png)

4.  右側的效能面板會顯示所選作業不同要求的持續時間分佈。  請將時間範圍縮小為從第 95 個百分位數開始。 從「前 3 大相依性」深入解析卡片您可以很快地看出，外部相依性很可能是交易緩慢的主因。  按一下附有範例數目的按鈕，以檢視範例清單。 接著，您可以選取任何範例以檢視交易詳細資料。

5.  您可以一眼看出，交易的總持續時間最主要來自於 Fabrikamaccount Azure 資料表。 您也可以查看導致交易失敗的例外狀況。 您可以按一下清單中的任何項目，以在右側查看其詳細資料。 [深入了解交易診斷體驗](../../azure-monitor/app/transaction-diagnostics.md)

    ![作業端對端詳細資料](media/tutorial-performance/4-end-to-end.png)
    

6.  **Profiler** 可顯示為作業執行的實際程式碼和每個步驟所需的時間，以協助您深入進行程式碼層級的診斷。 因為 Profiler 是定期執行，某些作業可能不具有追蹤。  隨著時間累積，將會有更多作業具有追蹤。  若要針對作業啟動 Profiler，請按一下 [Profiler 追蹤]  。
5.  追蹤會顯示每個作業的個別事件，讓您可以診斷整體作業持續時間的根本原因。  按一下持續時間最長之前幾個範例的其中一個。
6.  按一下 [最忙碌路徑]  來反白顯示對於作業的總持續時間影響最深事件的特定路徑。  在此範例中，您可以看到最慢的呼叫是來自 FabrikamFiberAzureStorage.GetStorageTableData  方法。 花費最多時間的部分是 CloudTable.CreateIfNotExist  方法。 如果每次呼叫函式時都會執行此程式碼，就會使用不必要的網路呼叫和 CPU 資源。 修正程式碼的最佳方式，是將這一行放在只執行一次的某些啟動方法中。

    ![Profiler 詳細資料](media/tutorial-performance/5-hot-path.png)

7.  畫面頂端的 [效能提示]  證實了過長的持續時間是由於等待。  按一下 [等待]  連結以存取關於解譯不同事件類型的文件。

    ![效能提示](media/tutorial-performance/6-perf-tip.png)

8.   如需進一步分析，您可按一下 [下載追蹤]  來下載追蹤。 您可以使用 [PerfView](https://github.com/Microsoft/perfview#perfview-overview)來檢視此資料。

## <a name="use-logs-data-for-server"></a>使用伺服器的記錄資料
 提供豐富的查詢語言，可讓您分析 Application Insights 收集的所有資料。 您可以使用這項功能來深入分析要求和效能資料。

1. 返回 [作業詳細資料] 面板，然後按一下![記錄圖示](media/tutorial-performance/app-viewinlogs-icon.png)**[記錄中的檢視 (分析)]**

2. 記錄隨即開啟，面板中每個檢視各有一個查詢。  您可以依原狀執行這些查詢，或根據您的需求作修改。  第一個查詢顯示這項作業過去的持續時間。

    ![記錄查詢](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>識別緩慢的用戶端作業
除了識別要最佳化的伺服器處理序，Application Insights 還可以從用戶端瀏覽器的角度來進行分析。  這可協助您識別用戶端元件是否有改善空間，甚至識別不同瀏覽器或不同位置的問題。

1. 選取 [調查] 底下的**瀏覽器**，然後按一下 [瀏覽器效能]，或按一下右上方的伺服器/瀏覽器切換按鈕，即可選取 [調查] 底下的 [效能]，並切換至 [瀏覽器] 索引標籤，以開啟瀏覽器效能摘要。 這樣會從瀏覽器的角度提供各種應用程式遙測的視覺效果摘要。

    ![瀏覽器摘要](media/tutorial-performance/8-browser.png)

2. 選取其中一個作業名稱，然後按一下右下方的藍色範例按鈕，然後選取作業。 這會顯示端對端交易詳細資料，而您可以在右側檢視**網頁檢視屬性**。 這可讓您檢視要求頁面用戶端的詳細資料，包括瀏覽器的類型和其位置。 這項資訊有助於判斷是否有和特定類型的用戶端相關的效能問題。

    ![頁面檢視](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>使用用戶端的記錄資料
就像針對伺服器效能所收集的資料一樣，Application Insights 讓所有用戶端資料可供使用記錄進行深入分析。

1. 返回瀏覽器摘要，然後按一下![記錄圖示](media/tutorial-performance/app-viewinlogs-icon.png) [記錄中的檢視 (分析)] 

2. 記錄隨即開啟，面板中每個檢視各有一個查詢。 第一個查詢顯示不同的頁面檢視過去的持續時間。

    ![記錄查詢](media/tutorial-performance/10-page-view-logs.png)

3.  「智慧型診斷」是記錄的一項功能，可識別資料中的唯一模式。 當您按一下折線圖中「智慧型診斷」點時，將會執行相同的查詢，但不涵蓋造成異常的記錄。 這些記錄的詳細資料顯示在查詢的註解區段，讓您可以針對導致過長持續時間的頁面檢視，識別這些頁面檢視的屬性。

    ![具有智慧型診斷的記錄](media/tutorial-performance/11-page-view-logs-dsmart.png)


## <a name="next-steps"></a>後續步驟
由於您已學會如何識別執行階段例外狀況，請前進到下一個教學課程，了解如何建立警示以回應失敗。

> [!div class="nextstepaction"]
> [針對應用程式健康情況發出警示](../../azure-monitor/learn/tutorial-alert.md)
