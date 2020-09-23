---
title: 升級 Log Analytics 儀表板視覺效果
description: 瞭解如何使用可提供強大見解的查詢來升級 Log Analytics 儀表板視覺效果。
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: a029dcbebf6dfe7a2b6cb517641c824a5937ca95
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90988250"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>升級 Log Analytics 儀表板視覺效果

在2020年2月，我們引進了改良的視覺效果技術。 改善和強化視覺化查詢結果的能力，並快速觸及強大的見解。 

您可以在此 [Azure 更新](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/)中閱讀此升級的詳細資訊。 

這項新的視覺效果技術 paving 了在查詢結果集的全新和改良體驗方面。 

## <a name="dashboards-in-azure"></a>Azure 中的儀表板

Azure 儀表板可讓您以視覺化方式呈現整個 Azure 介面區的狀態。 其設計目的是為您的 Azure 資產狀態提供單一窗格，並允許常見動作的各種快捷方式。 

如需詳細資訊，請參閱 [Azure 儀表板](../../azure-portal/azure-portal-dashboards.md)


## <a name="upgrading-log-analytics-dashboard-parts"></a>升級 Log Analytics 儀表板元件

新的視覺效果技術解決了舊版的一些常見問題，並為釘選的 Log Analytics 元件引進一些新功能： 

- **相同的可用類型** -所有可在 Log Analytics 中使用的視覺效果類型，都可作為儀表板上的固定部分。

- **一致的外觀與風格** -釘選元件的視覺效果外觀和風格現在與 Log Analytics 中的元件幾乎完全相同。 這些差異是因為優化的緣故，需要視覺效果的資料內容有些許差異。 請參閱本檔的考慮部分，以深入瞭解這些差異。

- **工具提示和標籤** –新釘選的 Log Analytics 視覺效果支援工具提示。 圓形圖和環圈圖現在支援標籤。

- **互動式圖例** -按一下視覺效果圖例可在 Log Analytics 中新增/移除釘選視覺效果中的維度。

## <a name="stage-1---opt-in-upgrade-message"></a>階段 1-加入宣告升級訊息

當 Log Analytics 釘選的元件可以升級時，儀表板中的 Log Analytics 固定元件上會出現新的 *加入宣告* 通知，讓使用者可以升級其視覺效果。 如果您想要體驗新的視覺效果，以在儀表板中升級選取的視覺效果。

 
![資訊看板](media/dashboard-upgrade/update-message-1.png)
 
![資訊看板](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> 一旦發行儀表板，升級就無法復原。 但是，如果您離開儀表板而不需要重新發佈，就會捨棄變更。  

一旦按一下，視覺效果就會更新為新的技術。 視覺效果中的微妙差異可能會與記錄分析中的外觀和風格一致。

升級視覺效果之後，您必須重新發佈儀表板，變更才會生效。

![資訊看板](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>階段 2-所有儀表板的遷移

初始加入宣告期間結束後，Log Analytics 小組將會升級系統中的所有儀表板。 調整所有 Azure 儀表板可讓小組引進更多視覺效果，並在整個面板中體驗改進。

## <a name="considerations"></a>考量

釘選到儀表板的 Log Analytics 視覺效果有一些專為最佳體驗而設計的特定行為。 將視覺效果釘選到儀表板時，請參閱下列設計考慮。

### <a name="query-time-scope---30-day-limit"></a>查詢時間範圍-30 天限制

因為儀表板可能包含多個查詢的多個視覺效果，所以單一釘選查詢的時間範圍限制為 30 天。 單一查詢可能只會在較小或等於30天的時間範圍內執行。 這項限制是為了確保適當的儀表板載入時間。

### <a name="query-data-values---25-values-and-other-grouping"></a>查詢資料值-25 個值和其他群組

儀表板可以視覺效果密集且複雜。 為了在觀看儀表板時減少認知負載，我們會將顯示限制為25個不同的資料類型，以優化視覺效果。 當超過25個時，Log Analytics 會將資料優化。 它會個別顯示25個具有個別資料的類型，然後將其餘的值分組為「其他」值。 下圖顯示這類案例。  

![資訊看板](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>負載時的儀表板重新整理

載入時，會重新整理儀表板。 所有與儀表板固定的 Log Analytics 視覺效果相關的查詢都會執行，而且儀表板會在載入後重新整理。 如果 [儀表板] 頁面保持開啟，儀表板中的資料會每隔60分鐘重新整理一次。

## <a name="next-steps"></a>下一步

[在 Log Analytics 中建立和共用儀表板](../learn/tutorial-logs-dashboards.md)
