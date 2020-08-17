---
title: 建立和共用 Azure Log Analytics 資料的儀表板 | Microsoft Docs
description: 本教學課程可幫助您了解 Log Analytics 儀表板如何以視覺化方式呈現所有儲存的記錄查詢，讓您以單一方式檢視您的環境。
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 05/28/2020
ms.custom: mvc
ms.openlocfilehash: d38a4ba04529e70ecfd772eef7f60179041f0273
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927363"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>建立和共用 Log Analytics 資料的儀表板

Log Analytics 儀表板可以視覺化方式呈現所有儲存的記錄查詢，讓您可以尋找、相互關聯，並共用組織中的 IT 作業資料。  本教學課程涵蓋建立記錄查詢，其將用來支援由您的 IT 作業支援團隊存取的共用儀表板。  您會了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中建立共用儀表板
> * 以視覺化方式呈現效能記錄查詢 
> * 將記錄查詢新增至共用儀表板 
> * 在共用儀表板中自訂圖格

若要完成本教學課程中的範例，您目前必須有[連線至 Log Analytics 工作區](quick-collect-azurevm.md)的虛擬機器。  
 
## <a name="sign-in-to-azure-portal"></a>登入 Azure 入口網站
登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。 

## <a name="create-a-shared-dashboard"></a>建立共用儀表板
選取 [儀表板] 以開啟您的預設[儀表板](../../azure-portal/azure-portal-dashboards.md)。 您的儀表板外觀與以下範例不同。

![Azure 入口網站儀表板](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

您可以在這裡將所有 Azure 資源中對 IT 最重要的作業資料結合在一起，包括來自 Azure Log Analytics 的遙測資料。  在我們進入以視覺化方式呈現記錄查詢之前，請先建立並共用儀表板。  接著我們可以專注於範例效能記錄查詢，該查詢會轉譯為折線圖，然後將其新增至儀表板。  

> [!NOTE]
> 在使用記錄查詢的 Azure 儀表板中，系統支援下列圖表類型：
> - areachart
> - columnchart
> - piechart (在儀表板中會轉譯為環圈圖)
> - scatterchart
> - 時間圖

若要建立儀表板，請選取目前的儀表板名稱旁的 [新增儀表板]  按鈕。

![在 Azure 入口網站中建立新儀表板](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

這個動作會建立全新的空白私人儀表板並讓您進入自訂模式，供您為儀表板命名並新增或重新排列圖格。 編輯儀表板名稱，並指定此教學課程的「範例儀表板」，然後選取 [自訂完成]。<br><br> ![儲存自訂的 Azure 儀表板](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

當您建立儀表板時，它預設是私人用途，這表示只有您會看得到。 若要讓其他人也能看見，請使用與其他儀表板命令一同出現的 [共用]  按鈕。

![在 Azure 入口網站中共用新儀表板](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

系統會要求您選擇可將儀表板發佈至的訂用帳戶和資源群組。 為了方便起見，入口網站的發佈體驗會引導您朝向將儀表板放在稱為 **儀表板**的資源群組中的模式。  確認選取的訂用帳戶，然後按一下[發行]。  使用 [Azure 角色型存取控制 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) 來控制對儀表板中顯示資訊的存取權。   

## <a name="visualize-a-log-query"></a>以視覺化方式呈現記錄查詢
[Log Analytics](../log-query/get-started-portal.md) 是專用的入口網站，用來處理記錄查詢及其結果。 功能包括在多行上編輯查詢、選擇性執行程式碼、區分內容的 Intellisense 和 Smart Analytics。 在本教學課程中，您將會使用 Log Analytics，以圖形的形式建立效能檢視，儲存以用於未來查詢，然後將其釘選在稍早建立的共用儀表板。

藉由選取 [Azure 監視器] 功能表中的 [記錄] 來開啟 Log Analytics。 它會從新的空白查詢來開始。

![首頁](media/tutorial-logs-dashboards/homepage.png)

輸入下列查詢，只為 Windows 和 Linux 電腦傳回處理器使用率記錄，依電腦和 TimeGenerated 分組並顯示在視覺化圖表中。 按一下 [執行] 以執行查詢，並且檢視產生的圖表。

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

從頁面上方選取 [儲存]按鈕來儲存查詢。

![儲存查詢](media/tutorial-logs-dashboards/save-query.png)

在 [儲存查詢] 控制台中，提供像是「Azure VM - 處理器使用率」的名稱，和例如「儀表板」的分類，然後按一下 [儲存]。  這樣一來，您可以建立可使用及修改的常用查詢程式庫。  最後，從頁面右上角選取 [釘選到儀表板]，然後選取儀表板名稱，將此項目釘選在稍早建立的共用儀表板。

現在我們已將查詢釘選在儀表板，您會注意到其下方會有一般標題與註解。

![Azure 儀表板範例](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 我們應將它重新命名為有意義的名稱，讓檢視者容易了解。  按一下 [編輯] 按鈕以自訂圖格的標題和子標題，然後按一下 [更新]。  橫幅隨即出現，要求您發行變更或捨棄。  按一下 [儲存複本]。  

![已完成儀表板範例的設定](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何在 Azure 入口網站中建立儀表板，並新增記錄查詢。  前進至下一個教學課程，以了解您可以根據記錄查詢結果實作的不同回應。  

> [!div class="nextstepaction"]
> [使用 Log Analytics 警示來回應事件](tutorial-response.md)
