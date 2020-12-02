---
title: 在 Azure Application Insights 中建立自訂儀表板 | Microsoft Docs
description: 使用 Azure Application Insights 建立自訂 KPI 儀表板的教學課程。
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperfq1
ms.openlocfilehash: 31dd33bd8805ffcc677d5f0e98e81f2fa9e91ee2
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95537045"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>使用 Azure Application Insights 建立自訂 KPI 儀表板

您可以在 Azure 入口網站中建立多個儀表板，其中每個儀表板會針對來自不同資源群組和訂用帳戶的多重 Azure 資源，包含磚視覺化資料。  從 Azure Application Insights 釘選不同的圖表和檢視來建立自訂的儀表板，就能提供應用程式健康情況和效能的完整概況。 本教學課程會引導您建立自訂的儀表板，其內容包含來自 Azure Application Insights 的多種類型資料和視覺效果。

 您會了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立自訂的儀表板
> * 從磚庫新增磚
> * 將 Application Insights 中的標準計量加入儀表板
> * 將自訂計量圖表 Application Insights 加入儀表板
> * 將 Logs (Analytics) 查詢的結果加入儀表板

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

- 將 .NET 應用程式部署至 Azure，並[啟用 Application Insights SDK](../app/asp-net.md)。

> [!NOTE]
> 有關使用儀表板的必要權限，請參閱[了解儀表板的存取控制](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards)一文。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-new-dashboard"></a>建立新的儀表板

> [!WARNING]
> 如果您將應用程式深入解析資源移到不同的資源群組或訂用帳戶，則需要移除舊的圖格，並在新位置中相同的應用程式深入解析資源中釘選新的圖格，以手動方式更新儀表板。

單一儀表板可包含來自多個應用程式、資源群組和訂用帳戶的資源。  請從為您的應用程式建立新的儀表板，以開始本教學課程。  

1. 在 Azure 入口網站中左側的下拉式功能表中，選取 [儀表板]。

    ![Azure 入口網站下拉式功能表](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. 在 [儀表板] 窗格中，選取 [新增儀表板]，然後選取 [空白儀表板]。

   ![新增儀表板](media/tutorial-app-dashboards/new-dashboard.png)

3. 輸入儀表板的名稱。
4. 從 [磚庫] 內各式各樣的磚當中挑選要新增至您儀表板的磚。  除了從磚庫新增磚以外，您還可直接從 Application Insights 將圖表和其他檢視釘選至儀表板。
5. 找到 [Markdown] 磚，然後拖曳至您的儀表板。  這個磚可讓您加入 Markdown 格式的文字，適合用來將描述性文字加入儀表板。 若要深入了解，請參閱[在 Azure 儀表板上使用 Markdown 磚以顯示自訂內容](../../azure-portal/azure-portal-markdown-tile.md)。
6. 將文字加入磚的屬性，然後調整其在儀表板畫布上的大小。

    [![編輯 Markdown 磚](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. 選取畫面頂端的 [完成自訂] 以結束磚自訂模式。

## <a name="add-health-overview"></a>新增健康情況概觀

有靜態文字的儀表板沒那麼有趣，因此，現在要從 Application Insights 新增磚來顯示關於您應用程式的資訊。 您可以從 [磚庫] 新增 Application Insights 磚，或是直接從 Application Insights 畫面釘選它們。 這可讓您先設定您所熟悉的圖表和檢視，再將它們釘選至儀表板。  請先從新增應用程式的標準健康情況概觀開始。  這不需要設定，而且在儀表板中可以自訂的程度最少。


1. 選取首頁上的 **Application Insights** 資源。
2. 在 [概觀] 窗格中，選取釘選圖示 ![釘選圖示](media/tutorial-app-dashboards/pushpin.png)，將磚新增至儀表板。
3. 在 [釘選到儀表板] 索引標籤中，選取要在其中新增磚的儀表板，或建立一個新的儀表板。
 
3. 右上角會出現通知，表示您的磚已釘選到儀表板。  選取通知中的 [已釘選到儀表板] 以返回儀表板，或使用儀表板窗格。
4. 此磚現在已新增至儀表板。 選取 [編輯] 可變更磚的位置。 選取並將其拖曳到適當的位置，然後選取 [完成自訂]。 您的儀表板現在已經有一個包含有用資訊的磚。

    [![編輯模式中的儀表板](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>新增自訂的計量圖表

[計量] 面板可讓您將 Application Insights 長期以來所收集的計量圖表化，並提供選擇性的篩選和群組。  和 Application Insights 中的其他元素一樣，此圖表也可新增到儀表板。  要這樣做，您必須先進行一些自訂。

1. 選取首頁中的 **Application Insights** 資源。
1. 選取 [計量]。  
2. 已經有一個建立好的空白圖表，系統將提示您新增計量。  將計量新增至圖表，然後選擇性地加入篩選和群組。  下列範例顯示依成功狀態分組的伺服器要求數目。  這樣會提供一個含有成功和失敗要求的執行中檢視。

    [![新增計量](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. 選取右側的 [釘選到儀表板]。

3.  右上角會出現通知，表示您的磚已釘選到儀表板。 選取通知中的 [已釘選到儀表板] 以返回儀表板，或使用 [儀表板] 索引標籤。

4. 此磚現在已新增至儀表板。 選取 [編輯] 可變更磚的位置。 選取磚並將其拖曳到適當的位置，然後選取 [完成自訂]。

## <a name="add-logs-query"></a>新增記錄查詢

Azure Application Insights Logs 提供豐富的查詢語言，可讓您分析 Application Insights 收集的所有資料。 如同圖表和其他檢視，您可以將記錄查詢的輸出新增到儀表板。

1. 選取首頁中的 **Application Insights** 資源。
2. 選取 [監視] 下方左側的 [記錄]，開啟 [記錄] 索引標籤。
3. 輸入下列查詢會傳回最常要求的前 10 個頁面和其要求計數：

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. 選取 [執行] 以驗證查詢的結果。
5. 選取釘選圖示 ![[釘選] 圖示](media/tutorial-app-dashboards/pushpin.png) 然後選取您的儀表板名稱。

5. 返回儀表板之前，請新增另一個查詢，但是以圖表方式呈現，以觀察在儀表板中視覺化記錄查詢的不同方式。 請從下列查詢開始，此查詢會彙總有最多例外狀況的前 10 個作業。

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. 選取 [圖表]，然後變更為 [環圈圖] 以視覺化輸出。

    [![具有上述查詢的環圈圖](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. 選取釘選圖示 ![[釘選] 圖示](media/tutorial-app-dashboards/pushpin.png) (位於右上角) 將圖表釘選到儀表板，然後返回儀表板。
7. 您所選格式的查詢結果現在已新增到您的儀表板。 選取每個項目並將其拖曳到適當的位置，然後選取 [完成自訂]。
8. 選取鉛筆圖示 ![鉛筆圖示](media/tutorial-app-dashboards/pencil.png) (位於每個標題上) 以提供具描述性的標題。

## <a name="share-dashboard"></a>共用儀表板

1. 在儀表板頂端，選取 [共用] 以發佈您的變更。
2. 您也可以定義應具有儀表板存取權的特定使用者。 如需詳細資訊，請參閱[使用 Azure 角色型存取控制共用 Azure 儀表板](../../azure-portal/azure-portal-dashboard-share-access.md)。
3. 選取 [發佈]  。

## <a name="next-steps"></a>後續步驟

由於您已學會如何建立自訂儀表板，請看看含有個案研究的其餘 Application Insights 說明文件。

> [!div class="nextstepaction"]
> [深入診斷](../app/devops.md)
