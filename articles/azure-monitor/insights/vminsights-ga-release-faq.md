---
title: 虛擬機器 （GA） 常見問題的 Azure 監視器 |微軟文檔
description: 適用於 VM 的 Azure 監視器是結合了 Azure VM 作業系統健康情況與效能監控的 Azure 解決方案，其會自動探索應用程式元件和與其他資源的相依性，並且會對應它們之間的通訊。 本文回答了有關 GA 版本的常見問題。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283883"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>適用于 VM 的 Azure 監視器（GA） 常見問題
此通用可用性常見問題解答涵蓋 2019 年第 4 季度和 2020 年第 1 季度我們為 GA 做好準備時所做的更改。

## <a name="updates-for-azure-monitor-for-vms"></a>VM Azure 監視器的更新
2020 年 1 月，我們在 GA 發佈之前發佈了新版本的 VM Azure 監視器。 啟用 VM Azure 監視器的客戶現在將收到 GA 版本，但使用 2019 年第 4 季度和更早版本的 VM Azure 監視器的現有客戶將提示升級。 如果跨多個工作區部署大型部署，此常見問題解答提供了大規模執行升級的指導。


通過此升級，VM 效能資料的 Azure 監視器與[容器的 Azure 監視器](container-insights-overview.md)存儲在相同的*InsightsMetrics*表中，這樣您就更容易地查詢這兩個資料集。 此外，您還可以存儲我們無法存儲在以前使用的表中的更多不同的資料集。 

我們的效能檢視現在使用我們在*InsightsMetrics*表中存儲的資料。  如果您尚未升級以在工作區上使用最新的 VMInsights 解決方案，則圖表將不再顯示資訊。  您可以通過以下說明從我們的**入門**頁面進行升級。


## <a name="what-is-changing"></a>變更內容為何？
我們發佈了名為 VMInsights 的新解決方案，其中包括用於資料收集的其他功能，以及用於在日誌分析工作區中存儲此資料的新位置。 

過去，我們在工作區啟用了 ServiceMap 解決方案，並在日誌分析工作區中設置效能計數器，將資料發送到*Perf*表。 此新解決方案將資料發送到名為*InsightsMetrics 的*表，該表也由 Azure 監視器用於容器。 此表架構允許我們存儲與*Perf*表格式不相容的其他指標和服務資料集。

我們更新了性能圖表，以使用我們在*InsightsMetrics*表中存儲的資料。 您可以升級以使用"從我們的**入門"頁面的***"見解指標"* 表，如下所述。


## <a name="how-do-i-upgrade"></a>如何升級？
當日志分析工作區升級到最新版本的 Azure 監視器到 VM 時，它將升級附加到該工作區的每個 VM 上的依賴項代理。 每個需要升級的 VM 都將在 Azure 門戶中 VM 的 Azure 監視器中的"**入門"** 選項卡中標識。 當您選擇升級 VM 時，它將升級該 VM 的工作區以及連接到該工作區的任何其他 VM。 您可以選擇單個 VM 或多個 VM、資源組或訂閱。 

使用以下命令使用 PowerShell 升級工作區：

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>如果我安裝 VMInsights 解決方案，我應該如何處理工作區中的效能計數器？

以前為 VM 啟用 Azure 監視器的方法在工作區中使用了效能計數器。 當前版本將此資料存儲在名為 的`InsightsMetrics`表中。 如果您不再需要使用這些效能計數器，則可以選擇在工作區中禁用這些效能計數器。 

>[!NOTE]
>如果表中具有引用這些計數器的`Perf`警報規則，則需要更新它們以引用`InsightsMetrics`表中存儲的新資料。 請參閱我們的文檔，例如可以使用的日誌查詢，請參閱此表。
>

如果您決定保持啟用效能計數器，將根據 [日誌分析定價] 對在`Perf`表中引入和存儲的資料進行計費。https://azure.microsoft.com/pricing/details/monitor/)

## <a name="how-will-this-change-affect-my-alert-rules"></a>此更改將如何影響我的警報規則？

如果已創建查詢針對工作區中啟用的效能計數器`Perf`的表的`InsightsMetrics`[日誌警報](../platform/alerts-unified-log.md)，則應更新這些規則以引用該表。 `ServiceMapComputer_CL`本指南也適用于使用`ServiceMapProcess_CL`和 的任何日誌搜索規則，因為這些資料集正在移動到`VMComputer`和`VMProcess`表。

我們將更新此常見問題解答和文檔，以包括我們收集的資料集的示例日誌搜索警報規則。

## <a name="how-will-this-affect-my-bill"></a>這將如何影響我的帳單？

計費仍基於日誌分析工作區中引入和保留的資料。

我們收集的機器級效能資料相同，大小與`Perf`表中存儲的資料類似，並且成本大致相同。

## <a name="what-if-i-only-want-to-use-service-map"></a>如果我只想使用服務映射，該怎麼辦？

沒關係 在 Azure 監視器中查看有關即將更新的更新的 VM 時，將在 Azure 門戶中看到提示。 發佈後，您將收到一個提示，請求更新到新版本。 如果只想使用["地圖"](vminsights-maps.md)功能，可以選擇不升級並繼續使用 Azure 監視器中的"地圖"功能（用於 VM）以及從工作區或儀表板磁貼訪問的服務映射解決方案。

如果選擇手動啟用工作區中的效能計數器，則可以在 Azure 監視器查看的某些性能圖表中查看資料。 發佈新解決方案後，我們將更新性能圖表以查詢`InsightsMetrics`表中存儲的資料。 如果要查看這些圖表中該表中的資料，則需要升級到新版本的 VM Azure 監視器。

要將資料從`ServiceMapComputer_CL`中`ServiceMapProcess_CL`移動的更改將影響 VM 的服務映射和 Azure 監視器，因此您仍然需要規劃此更新。

如果您選擇不升級到**VMInsights**解決方案，我們將繼續提供我們性能活頁簿的舊版本，這些版本引用`Perf`表中的資料。  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>服務映射資料集是否會存儲在見解指標中？

如果使用這兩個解決方案，則資料集將不會重複。 這兩個產品`VMComputer`共用將存儲在（以前ServiceMapComputer_CL）、（`VMProcess`以前ServiceMapProcess_CL）`VMConnection`的資料集和`VMBoundPort`用於存儲我們收集的地圖資料集的表。  

該`InsightsMetrics`表將存儲我們收集的 VM、進程和服務資料集，並且僅在使用 AZURE 監視器進行 VM 和 VM 見解解決方案時才會填充這些資料集。 服務映射解決方案不會收集或存儲`InsightsMetrics`表中的資料。

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>如果我的工作區中包含服務映射和 VMInsights 解決方案，我是否會收取雙倍費用？

否，這兩個解決方案共用我們存儲在`VMComputer`中的地圖資料集（以前ServiceMapComputer_CL）、（`VMProcess`以前ServiceMapProcess_CL）、`VMConnection`和`VMBoundPort`。 如果工作區中同時包含兩個解決方案，則此資料不會收取雙重費用。

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>如果我刪除服務映射或 VMInsights 解決方案，它會刪除我的資料嗎？

否，這兩個解決方案共用我們存儲在`VMComputer`中的地圖資料集（以前ServiceMapComputer_CL）、（`VMProcess`以前ServiceMapProcess_CL）、`VMConnection`和`VMBoundPort`。 如果刪除其中一個解決方案，這些資料集會注意到仍有一個解決方案使用資料，並且它保留在日誌分析工作區中。 您需要從工作區中刪除這兩個解決方案，以便從工作區中刪除資料。

## <a name="health-feature-is-in-limited-public-preview"></a>運行狀況功能處於有限的公共預覽版

我們收到了客戶關於 VM 運行狀況功能集的很多回饋。 圍繞此功能，人們對其支援監控工作流的潛力非常感興趣。 我們計畫進行一系列更改，以添加功能並解決我們收到的回饋。 

為了儘量減少這些更改對新客戶的影響，我們已將此功能移到**有限的公共預覽版**中。 此更新發生在 2019 年 10 月。

我們計畫在 2020 年重新開機此運行狀況功能，因為 VM 的 Azure 監視器位於 GA 中。

## <a name="how-do-existing-customers-access-the-health-feature"></a>現有客戶如何訪問運行狀況功能？

使用 Health 功能的現有客戶將繼續訪問它，但不會向新客戶提供。  

要訪問該功能，可以將以下功能標誌`feature.vmhealth=true`添加到 Azure 門戶 URL [https://portal.azure.com](https://portal.azure.com)。 示例`https://portal.azure.com/?feature.vmhealth=true`。

您還可以使用此短 URL，自動設置要素標誌： [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)。

作為現有客戶，您可以繼續使用與運行狀況功能連接到現有工作區設置的 VM 上的運行狀況功能。  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>我現在將 VM 運行狀況與一個環境一起使用，並希望將其部署到新的環境

如果您是使用 Health 功能的現有客戶，並希望將其用於新的推出，請立即vminsights@microsoft.com聯繫我們，索取說明。

## <a name="next-steps"></a>後續步驟

若要了解有助於您監視虛擬機器的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。
