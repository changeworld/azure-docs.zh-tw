---
title: 在 Azure Application Insights 中建立自訂儀表板 | Microsoft Docs
description: 使用 Azure Application Insights 建立自訂 KPI 儀表板的教學課程。
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: 49984349ce8d9d1bc43778c0042dd21485eded15
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829674"
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
單一儀表板可包含來自多個應用程式、資源群組和訂用帳戶的資源。  請從為您的應用程式建立新的儀表板，以開始本教學課程。  

1. 在儀表板窗格中，選取 [新增儀表板]。

   ![新增儀表板](media/tutorial-app-dashboards/1newdashboard.png)

1. 輸入儀表板的名稱。
1. 從 [磚庫] 內各式各樣的磚當中挑選要新增至您儀表板的磚。  除了從磚庫新增磚以外，您還可直接從 Application Insights 將圖表和其他檢視釘選至儀表板。
1. 找到 [Markdown] 磚，然後拖曳至您的儀表板。  這個磚可讓您加入 Markdown 格式的文字，適合用來將描述性文字加入儀表板。
1. 將文字加入磚的屬性，然後調整其在儀表板畫布上的大小。
    
    ![編輯 Markdown 磚](media/tutorial-app-dashboards/2dashboard-text.png)

1. 按一下畫面頂端的 [完成自訂] 以結束磚自訂模式。

## <a name="add-health-overview"></a>新增健康情況概觀
有靜態文字的儀表板沒那麼有趣，因此，現在要從 Application Insights 新增磚來顯示關於您應用程式的資訊。  您可以從 [磚庫] 新增 Application Insights 磚，或是直接從 Application Insights 畫面釘選它們。  這可讓您先設定您所熟悉的圖表和檢視，再將它們釘選至儀表板。  請先從新增應用程式的標準健康情況概觀開始。  這不需要設定，而且在儀表板中可以自訂的程度最少。


1. 選取首頁上的 **Application Insights** 資源。
2. 在 [概觀] 窗格中，按一下圖釘圖示 ![圖釘圖示](media/tutorial-app-dashboards/pushpin.png)，將圖格新增至您最後檢視的儀表板。  
 
3. 右上角會出現通知，表示您的磚已釘選到儀表板。 按一下通知中的 [已釘選到儀表板] 以返回儀表板，或使用儀表板窗格。
4. 此磚現在已新增至儀表板。 選取 [編輯] 可變更磚的位置。 按一下並將其拖曳到適當的位置，然後按一下 [完成自訂]。 您的儀表板現在已經有一個包含有用資訊的磚。

    ![含有 [概觀時間軸] 的儀表板](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>新增自訂的計量圖表
[計量] 面板可讓您將 Application Insights 長期以來所收集的計量圖表化，並提供選擇性的篩選和群組。  和 Application Insights 中的其他元素一樣，此圖表也可新增到儀表板。  要這樣做，您必須先進行一些自訂。

1. 選取首頁中的 **Application Insights** 資源。
1. 選取 [計量]。  
2. 已經有一個建立好的空白圖表，系統將提示您新增計量。  將計量新增至圖表，然後選擇性地加入篩選和群組。  下列範例顯示依成功狀態分組的伺服器要求數目。  這樣會提供一個含有成功和失敗要求的執行中檢視。

    ![新增計量](media/tutorial-app-dashboards/metrics.png)

4. 選取右側的 [釘選到儀表板]。 這樣會將檢視新增到您所檢視的最後一個儀表板。

3.  右上角會出現通知，表示您的磚已釘選到儀表板。 按一下通知中的 [已釘選到儀表板] 以返回儀表板，或使用儀表板刀鋒視窗。

4. 此磚現在已新增至儀表板。 選取 [編輯] 可變更磚的位置。 按一下並將其拖曳到適當的位置，然後按一下 [完成自訂]。

## <a name="add-logs-analytics-query"></a>新增 Logs (Analytics) 查詢
Azure Application Insights Logs (Analytics) 提供豐富的查詢語言，可讓您分析 Application Insights 收集的所有資料。 如同圖表和其他檢視，您可以將記錄查詢的輸出新增到儀表板。

Azure Applications Insights Logs (Analytics) 是分開的服務，因此您必須共用儀表板，儀表板才能包含記錄查詢。 當您共用 Azure 儀表板時，便是將它發行為 Azure 資源，這樣就能將它提供給其他使用者和資源。  

1. 在儀表板畫面的頂端，按一下 [共用]。

    ![發佈儀表板](media/tutorial-app-dashboards/8dashboard-share.png)

2. 將 [儀表板名稱] 維持不變，然後選取 [訂用帳戶名稱] 以共用儀表板。  按一下 [發佈] 。  儀表板現在已可供其他服務和訂用帳戶使用。  您也可以定義應具有儀表板存取權的特定使用者。
1. 選取首頁中的 **Application Insights** 資源。
2. 在監視底下按一下左側的 [Logs (Analytics)]，以開啟 Logs (Analytics) 入口網站。
3. 輸入下列查詢會傳回最常要求的前 10 個頁面和其要求計數：

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. 按一下 [執行] 以驗證查詢的結果。
5. 按一下 [釘選] 圖示 ![[釘選] 圖示](media/tutorial-app-dashboards/pushpin.png) 然後選取您的儀表板名稱。 有別於先前的步驟使用的是最後一個儀表板，這個選項之所以讓您選取儀表板，是因為 Logs (Analytics) 主控台是分開的服務，因此需從所有可用的共用儀表板中選取。

5. 返回儀表板之前，請新增另一個查詢，但這次將它以圖形方式呈現，以觀察在儀表板中視覺化記錄查詢的不同方式。 請從下列查詢開始，此查詢會彙總有最多例外狀況的前 10 個作業。

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. 選取 [圖表]，然後變更為 [環圈圖] 以視覺化輸出。

    ![Logs (Analytics) 圖表](media/tutorial-app-dashboards/11querychart.png)

6. 按一下 [釘選] 圖示 ![[釘選] 圖示](media/tutorial-app-dashboards/pushpin.png) (位於右上角) 以將圖表釘選至儀表板，這次請選取連結以返回儀表板。
4. 您所選格式的查詢結果現在已新增到您的儀表板。  按一下並將每個項目拖曳到適當的位置，然後按一下 [完成自訂]。
5. 選取鉛筆圖示 ![鉛筆圖示](media/tutorial-app-dashboards/pencil.png) (位於每個標題上) 以提供具描述性的標題。

5. 選取 [共用] 以將變更重新發佈到您的儀表板。您的儀表板現在包含了各種來自 Application Insights 的圖表和視覺效果。


## <a name="next-steps"></a>後續步驟
由於您已學會如何建立自訂儀表板，請看看含有個案研究的其餘 Application Insights 說明文件。

> [!div class="nextstepaction"]
> [深入診斷](../app/devops.md)