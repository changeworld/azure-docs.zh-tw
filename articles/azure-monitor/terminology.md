---
title: Azure 監視器術語更新 | Microsoft Docs
description: 描述最近對 Azure 監視服務所做的術語變更。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 0c5e1096b0780d2f9e50389f772e63344935012b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274134"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure 監視器命名和術語變更
最近已對 Azure 監視器進行了重大變更，合併了不同服務以簡化對 Azure 客戶的監視。 本文說明 Azure 監視器文件中的最新名稱和術語變更。

## <a name="october-2019---diagnostic-log-to-resource-log"></a>2019年10月-診斷記錄到資源記錄檔
「診斷記錄」已變更為「資源記錄」，使其更符合實際收集的內容。 「診斷設定」一詞維持不變。  

## <a name="february-2019---log-analytics-terminology"></a>2019 年 2 月 - Log Analytics 術語
在 Azure 監視器下彙總不同服務之後，我們將藉由修改文件中的術語來更明確地描述 Azure 監視器服務及其不同的元件，從而採取下一個步驟。 

### <a name="log-analytics"></a>Log Analytics
Azure 監視器記錄資料仍儲存在 Log Analytics 工作區中，且仍由相同的 Log Analytics 服務收集及分析，但我們正在將許多地方中的字詞 _Log Analytics_ 變更為 _Azure 監視器記錄_。 這個詞彙更能在 Azure 監視器中反映其角色，並可與[Azure 監視器中的計量](platform/data-platform-metrics.md)提供較佳的一致性。

字詞_記錄分析_現在主要應用於在 Azure 入口網站中撰寫和執行查詢和分析記錄資料頁面。 它在功能上與[計量瀏覽器](platform/metrics-charts.md)相同，計量瀏覽器是 Azure 入口網站中用於分析計量資料的頁面。

### <a name="log-analytics-workspaces"></a>Log Analytics 工作區
[在 Azure 監視器中儲存記錄資料的工作區](platform/manage-access.md)仍然稱為 Log Analytics 工作區。 Azure 入口網站中的 [Log Analytics] 功能表已重新命名為 [Log Analytics 工作區]，您可以在其中[建立新的工作區](learn/quick-create-workspace.md)及設定資料來源。 分析 **Azure 監視器**中的記錄和其他監視資料，並在 **Log Analytics 工作區**中設定您的工作區。

### <a name="management-solutions"></a>管理解決方案
[管理解決方案](insights/solutions.md)已重新命名為_監視解決方案_，更清楚地描述了其功能。


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>2018 年 8 月 - 將監視服務合併至 Azure 監視器
Log Analytics 和 Application Insights 已合併到 Azure 監視器，以針對監視 Azure 資源和混合式環境提供單一整合體驗。 這些服務並未移除任何功能，使用者可以執行的案例和以往所完成的一樣，既不會有任何損失，功能也不會打折。

上述各項服務的文件已合併成一組單一的 Azure 監視器內容。 這有助於讀者在單一位置就能找到特定監視案例的所有內容，而不需要參考多組內容。 隨著合併後服務的發展，裡面內容的整合程度會提升。

屬於 Log Analytics 一部分的其他功能 (例如代理程式和檢視)，則會重新定位為 Azure 監視器的功能。 除了在 Azure 入口網站中的體驗會有潛在改善外，這些功能的作用並沒有什麼改變。


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>2018 年 4 月 - 淘汰 Operations Management Suite 品牌
以往基於授權需要，下列 Azure 管理服務會隨附 Operations Management Suite (OMS)：

- Application Insights
- Azure 自動化
- Azure 備份
- Log Analytics
- Site Recovery

[這些服務已採用新的定價](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)，且 OMS 統合不再適用於新的客戶。 除了如上所述合併到 Azure 監視器外，屬於 OMS 一部分的服務皆未變更。 




## <a name="next-steps"></a>後續步驟

- 請閱讀 [Azure 監視器的概觀](overview.md)，當中會描述其不同的元件與功能。
- 了解 [OMS 入口網站的轉換](../log-analytics/log-analytics-oms-portal-transition.md)。
