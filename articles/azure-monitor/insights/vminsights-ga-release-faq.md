---
title: 適用於 VM 的 Azure 監視器 (GA) 常見問題 |Microsoft Docs
description: 適用於 VM 的 Azure 監視器是結合了 Azure VM 作業系統健康情況與效能監控的 Azure 解決方案，其會自動探索應用程式元件和與其他資源的相依性，並且會對應它們之間的通訊。 本文將回答有關 GA 版本的常見問題。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: be192a5807c40ea65ea6533ec6244183e5a4b644
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958791"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>適用於 VM 的 Azure 監視器正式推出 (GA) 常見問題
此正式運作常見問題涵蓋在我們為 GA 做好準備時，在2019和 Q1 2020 中所做的變更。

## <a name="updates-for-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器的更新
我們已在 GA 宣佈之前，于2020年1月發行新版本的適用於 VM 的 Azure 監視器。 啟用適用於 VM 的 Azure 監視器的客戶現在將會收到 GA 版本，但使用2019版及更早版本的適用於 VM 的 Azure 監視器版本的現有客戶將會收到升級的提示。 如果您有跨多個工作區的大型部署，此常見問題提供了大規模升級的指引。


透過這項升級，適用於 VM 的 Azure 監視器的效能資料會儲存在與[容器 Azure 監視器](container-insights-overview.md)相同的*InsightsMetrics*資料表中，讓您更輕鬆地查詢兩個資料集。 此外，您還能儲存較多不同的資料集，我們無法儲存在先前使用的資料表中。 

我們的效能觀點現在會使用我們儲存在 *InsightsMetrics* 資料表中的資料。  如果您的工作區尚未升級為使用最新的 VMInsights 解決方案，則您的圖表將不會再顯示資訊。  您可以從我們的 **開始** 頁面進行升級，如下所述。


## <a name="what-is-changing"></a>變更內容為何？
我們發行了名為 VMInsights 的新解決方案，其中包含資料收集的額外功能，以及將此資料儲存在 Log Analytics 工作區中的新位置。 

在過去，我們已在您的工作區上啟用 ServiceMap 解決方案，並在您的 Log Analytics 工作區中設定效能計數器，以將資料傳送至*效能資料表。* 這個新的解決方案會將資料傳送至名為 *InsightsMetrics* 的資料表，此資料表也會由 Azure 監視器用於容器。 此資料表架構可讓我們儲存與 *效能資料表格式不相容的其他* 計量和服務資料集。

我們已更新我們的效能圖表，以使用我們儲存在 *InsightsMetrics* 資料表中的資料。 您可以升級為使用**開始**頁面中的*InsightsMetrics*資料表，如下所述。


## <a name="how-do-i-upgrade"></a>如何? 升級？
當 Log Analytics 工作區升級為最新版的 Azure 監視器到 Vm 時，它會在連接到該工作區的每個 Vm 上升級相依性代理程式。 每個需要升級的 VM 將會在 Azure 入口網站中適用於 VM 的 Azure 監視器的 [ **開始** ] 索引標籤中識別。 當您選擇升級 VM 時，它會將該 VM 的工作區連同任何其他附加至該工作區的 Vm 進行升級。 您可以選取單一 VM 或多個 vm、資源群組或訂用帳戶。 

使用下列命令來使用 PowerShell 升級工作區：

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>如果我安裝 VMInsights 解決方案，我應該如何處理我的工作區中的效能計數器？

上一個啟用適用於 VM 的 Azure 監視器的方法是在您的工作區中使用效能計數器。 目前的版本會將此資料儲存在名為的資料表中 `InsightsMetrics` 。 如果您不再需要使用這些效能計數器，您可以選擇在工作區中停用這些效能計數器。 

>[!NOTE]
>如果您有在資料表中參考這些計數器的警示規則 `Perf` ，您必須將它們更新為參考資料表中所儲存的新資料 `InsightsMetrics` 。 請參閱我們的檔，以取得您可以用來參考此資料表的範例記錄查詢。
>

如果您決定要讓效能計數器保持啟用狀態，則會向您收取資料內嵌的費用，並 `Perf` 根據 [Log Analytics 定價 [ (] 儲存在資料表中 https://azure.microsoft.com/pricing/details/monitor/) 。

## <a name="how-will-this-change-affect-my-alert-rules"></a>這種變更如何影響我的警示規則？

如果您已建立 [記錄警示](../platform/alerts-unified-log.md) ，以查詢 `Perf` 工作區中已啟用的效能計數器資料表，您應該更新這些規則以改為參考 `InsightsMetrics` 資料表。 本指南也適用于使用和的任何記錄搜尋規則 `ServiceMapComputer_CL` `ServiceMapProcess_CL` ，因為這些資料集會移至 `VMComputer` 和 `VMProcess` 資料表。

我們將更新此常見問題和我們的檔，以包含我們所收集之資料集的範例記錄搜尋警示規則。

## <a name="how-will-this-affect-my-bill"></a>這會對我的帳單有何影響？

依資料內嵌計費，並保留在您的 Log Analytics 工作區中。

我們所收集的機器層級效能資料是相同的，其大小與我們儲存在資料表中的資料大小類似， `Perf` 而且成本大約會是相同的數量。

## <a name="what-if-i-only-want-to-use-service-map"></a>如果我只想使用服務對應，該怎麼辦？

沒關係。 查看即將進行之更新的適用於 VM 的 Azure 監視器時，您會在 Azure 入口網站中看到提示。 發行之後，您會收到要求您更新至新版本的提示。 如果您只想要使用 [ [對應](vminsights-maps.md) ] 功能，您可以選擇不升級並繼續使用適用於 VM 的 Azure 監視器中的 [對應] 功能，以及從您的工作區或儀表板磚存取的服務對應解決方案。

如果您選擇以手動方式在工作區中啟用效能計數器，您可以在我們從 Azure 監視器觀看的部分效能圖表中看到資料。 一旦發行新的解決方案之後，我們將會更新我們的效能圖表，以查詢儲存在資料表中的資料 `InsightsMetrics` 。 如果您想要在這些圖表中看到該資料表的資料，您將需要升級至新版本的適用於 VM 的 Azure 監視器。

移動資料的變更將會 `ServiceMapComputer_CL` `ServiceMapProcess_CL` 影響服務對應和適用於 VM 的 Azure 監視器，因此您仍然需要規劃這項更新。

如果您選擇不升級至 **VMInsights** 解決方案，我們將會繼續提供舊版的效能活頁簿，這些活頁簿會參考資料表中的資料 `Perf` 。  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>服務對應資料集是否也會儲存在 InsightsMetrics 中？

如果您同時使用這兩個方案，則不會複製資料集。 這兩個供應專案都會共用將儲存在 `VMComputer` (先前 ServiceMapComputer_CL) 中的資料集， `VMProcess` (先前 ServiceMapProcess_CL) 、 `VMConnection` 和資料表， `VMBoundPort` 以儲存我們所收集的地圖資料集。  

`InsightsMetrics`資料表將會儲存我們所收集的 VM、進程和服務資料集，而且只有在您使用適用於 VM 的 Azure 監視器和 VM 深入解析解決方案時，才會填入這些資料集。 服務對應的解決方案不會在資料表中收集或儲存資料 `InsightsMetrics` 。

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>如果我的工作區中有服務對應和 VMInsights 的解決方案，是否需要支付費用？

否，這兩個解決方案會共用儲存在 `VMComputer` (先前 ServiceMapComputer_CL) 的地圖資料集， `VMProcess` (先前 ServiceMapProcess_CL) 、 `VMConnection` 和 `VMBoundPort` 。 如果您的工作區中有兩個解決方案，您將不會對此資料進行雙重計費。

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>如果我移除服務對應或 VMInsights 方案，將會移除我的資料嗎？

否，這兩個解決方案會共用儲存在 `VMComputer` (先前 ServiceMapComputer_CL) 的地圖資料集， `VMProcess` (先前 ServiceMapProcess_CL) 、 `VMConnection` 和 `VMBoundPort` 。 如果您移除其中一個解決方案，這些資料集會發現仍有使用資料的解決方案，且它會保留在 Log Analytics 工作區中。 您必須從您的工作區移除這兩個解決方案，才能從中移除資料。

## <a name="health-feature-is-in-limited-public-preview"></a>健康情況功能處於有限的公開預覽階段

我們從客戶收到了許多關於 VM 健康情況功能集的絕佳意見反應。 這項功能與支援監視工作流程的潛力有很大的興趣。 我們打算進行一系列變更來新增功能，並解決我們所收到的意見反應。 

為了將這些變更對新客戶的影響降到最低，我們已將此功能移至 **有限的公開預覽**。 此更新于2019年10月發生。

我們計畫在適用於 VM 的 Azure 監視器正式運作後，于2020中重新開機此健康情況功能。

## <a name="how-do-existing-customers-access-the-health-feature"></a>現有客戶如何存取健康情況功能？

使用健康情況功能的現有客戶將可繼續存取它，但不會提供給新客戶。  

若要存取此功能，您可以在 Azure 入口網站 URL 中新增下列功能旗標 `feature.vmhealth=true` [https://portal.azure.com](https://portal.azure.com) 。 範例 `https://portal.azure.com/?feature.vmhealth=true` 。

您也可以使用這個簡短的 url，它會自動設定功能旗標： [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview) 。

以現有的客戶而言，您可以繼續在使用健康情況功能連線到現有工作區設定的 Vm 上使用健康情況功能。  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>我現在使用 VM 健康情況與一個環境，並想要將其部署到新環境

如果您是使用健康情況功能的現有客戶，而且想要將它用於新的推出，請與我們聯絡 vminsights@microsoft.com 以要求指示。

## <a name="next-steps"></a>後續步驟

若要了解有助於您監視虛擬機器的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。
