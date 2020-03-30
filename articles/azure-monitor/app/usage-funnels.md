---
title: Azure Application Insights 漏斗圖
description: 了解如何使用漏斗圖來探索客戶與您的應用程式互動的方式。
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 89440a6385bab5b917a866b686e8d2ba828c92e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671047"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>使用 Application Insights 漏斗圖來探索客戶如何使用您的應用程式

了解客戶體驗對於您的企業非常重要。 如果您的應用程式牽涉到多個階段，您需要了解大部分的客戶會處理整個程序，或者他們會在某個時間點結束處理程序。 在 Web 應用程式中通過一系列步驟的進展稱為*漏斗*。 您可以使用 Azure Application Insights 漏斗圖來深入了解使用者，並監視各步驟的轉換率。 

## <a name="create-your-funnel"></a>建立您的漏斗圖
建立您的漏斗圖之前，請決定要獲得回答的問題。 例如，您可能想要知道有多少使用者檢視首頁、檢視客戶設定檔和建立票證。 在此範例中，Fabrikam Fiber 公司的擁有者想要知道已成功建立客戶票證的客戶百分比。

以下是他們用來建立漏斗圖所採取的步驟。

1. 在 Application Insights 漏斗圖工具中，選取 [新增]****。
1. 從 [時間範圍]**** 下拉式清單中，選取 [過去 90 天]****。 選取 [我的漏斗圖]**** 或 [共用的漏斗圖]****。
1. 從 [步驟 1]**** 下拉式清單中，選取 [索引]****。 
1. 從 [步驟 2]**** 下拉式清單中，選取 [客戶]****。
1. 從 [步驟 3]**** 下拉式清單中，選取 [建立]****。
1. 新增漏斗圖的名稱，然後選取 [儲存]****。

以下螢幕擷取畫面顯示漏斗圖工具產生的資料類型範例。 Fabrikam 擁有者可以看到在過去 90 天期間，54.3% 的客戶已造訪首頁、建立客戶票證。 他們也會看到 2,700 個客戶從首頁來到索引。 這可能表示重新整理問題。


![具有資料的漏斗圖工具螢幕擷取畫面](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>漏斗圖功能
上述螢幕擷取畫面包含五個醒目顯示的區域。 這些是漏斗圖的功能。 下列清單進一步說明螢幕擷取畫面中的每個對應區域：
1. 如果您的應用程式進行取樣，您會看到取樣橫幅。 選取橫幅會開啟內容窗格，說明要如何關閉取樣。 
2. 您可以將漏斗圖匯出至 [Power BI](../../azure-monitor/app/export-power-bi.md )。
3. 選取步驟即可在右側查看詳細資料。 
4. 歷程記錄轉換圖表會顯示過去 90 天的轉換率。 
5. 存取使用者工具，以更加了解您的使用者。 您可以在每個步驟中使用篩選。 

## <a name="next-steps"></a>後續步驟
  * [使用量概觀](usage-overview.md)
  * [使用者、工作階段和事件](usage-segmentation.md)
  * [保留](usage-retention.md)
  * [活頁簿](../../azure-monitor/app/usage-workbooks.md)
  * [新增使用者內容](usage-send-user-context.md)
  * [匯出至 Power BI](../../azure-monitor/app/export-power-bi.md )

