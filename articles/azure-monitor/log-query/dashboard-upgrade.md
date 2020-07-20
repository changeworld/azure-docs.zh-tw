---
title: 升級您的 Log Analytics 儀表板視覺效果
description: 您可以從開始，並針對您的需求進行修改的查詢
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 6fb9f691afc8874b19ca4575408027357f07cc89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85833531"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>升級您的 Log Analytics 儀表板視覺效果

我們在2020年2月引進了改良的視覺效果技術。 改善和增強您視覺化查詢結果的能力，並快速觸及強大的見解。 

您可以在此[Azure 更新](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/)中閱讀此升級的詳細資訊。 

這個新的視覺效果技術是助長在查詢結果集的全新和改進體驗方面。 

## <a name="dashboards-in-azure"></a>Azure 中的儀表板

Azure 儀表板可讓您以視覺化方式呈現整個 Azure 介面區的狀態。 其設計目的是要為您的 Azure 資產狀態提供單一窗格，並允許常用動作的各種快捷方式。 

如需詳細資訊，請參閱[Azure 儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)


## <a name="upgrading-log-analytics-dashboard-parts"></a>升級 Log Analytics 儀表板元件

新的視覺效果技術解決了舊版執行的一些常見問題，並為固定的 Log Analytics 元件引進一些新功能： 

- **相同的可用類型**-Log Analytics 中所有可用的視覺效果類型，都可當做儀表板上的固定元件來使用。

- **一致的外觀與風格**-固定元件的視覺效果外觀與風格幾乎與 Log Analytics 中的相同。 其差異是因為優化需要在視覺效果的資料內容中有細微的差異。 請參閱本檔的考慮部分，以深入瞭解這些差異。

- **工具提示和標籤**-新釘選的 Log Analytics 視覺效果支援工具提示。 圓形圖和環圈圖現在支援標籤。

- **互動式圖例**–按一下 [視覺效果圖例] 可讓您在 Log Analytics 中新增/移除釘選視覺效果中的維度。

## <a name="stage-1---opt-in-upgrade-message"></a>階段 1-加入宣告升級訊息

當 Log Analytics 釘選的元件能夠升級時，新的*加入宣告*通知會出現在儀表板中的 Log analytics 釘選部分，讓使用者能夠升級其視覺效果。 如果您想要體驗新的視覺效果，以在其儀表板中升級選取的視覺效果。

 
![資訊看板](media/dashboard-upgrade/update-message-1.png)
 
![資訊看板](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> 發佈儀表板之後，升級就無法復原。 不過，如果您離開儀表板而不重新發行，則會捨棄變更。  

一旦按一下，視覺效果就會更新為新的技術。 視覺效果中的細微差異可能會與 Log Analytics 中的外觀和風格一致。

視覺效果升級之後，您必須重新發佈儀表板，變更才會生效。

![資訊看板](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>第2階段-所有儀表板的遷移

在初始加入宣告期間結束後，Log Analytics 小組會升級系統中的所有儀表板。 調整所有 Azure 儀表板可讓小組在整個面板中引進更多的視覺效果和經驗改進。

## <a name="considerations"></a>考量

釘選到儀表板的 Log Analytics 視覺效果具有專為最佳體驗而設計的特定行為。 將視覺效果釘選到儀表板時，請參閱下列設計考慮。

### <a name="query-time-scope---30-day-limit"></a>查詢時間範圍-30 天限制

因為儀表板可能包含多個查詢的多個視覺效果，所以單一釘選查詢的時間範圍限制為30天。 單一查詢只能在較小或等於30天的時間範圍內執行。 這項限制是為了確保有合理的儀表板載入時間。

### <a name="query-data-values---25-values-and-other-grouping"></a>查詢資料值-25 個值和其他群組

儀表板的視覺效果非常密集且複雜。 為了減少觀賞儀表板時的認知負載，我們會將顯示限制為25種不同的資料類型，以將視覺效果優化。 當有超過25個時，Log Analytics 會將資料優化。 它會個別顯示25個類型，其中大部分資料都是個別的，然後將其餘的值分組為「其他」值。 下圖顯示這種情況。  

![資訊看板](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>載入時重新整理儀表板

載入時，會重新整理儀表板。 所有與儀表板釘選的記錄分析視覺效果相關的查詢都會執行，而且儀表板會在載入後重新整理。 如果 [儀表板] 頁面保持開啟，儀表板中的資料會每60分鐘重新整理一次。

## <a name="next-steps"></a>後續步驟

[在 Log Analytics 中建立和共用儀表板](../learn/tutorial-logs-dashboards.md)
