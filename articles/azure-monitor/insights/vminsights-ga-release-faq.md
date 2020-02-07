---
title: 適用於 VM 的 Azure 監視器（GA）常見問題 |Microsoft Docs
description: 適用於 VM 的 Azure 監視器是結合了 Azure VM 作業系統健康情況與效能監控的 Azure 解決方案，其會自動探索應用程式元件和與其他資源的相依性，並且會對應它們之間的通訊。 本文會回答有關 GA 版本的常見問題。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: ea7c695ddb92d441018503839b974c1f4bb33473
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047853"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>適用於 VM 的 Azure 監視器正式推出（GA）的常見問題

此公開上市常見問題涵蓋在我們為 GA 版本做準備時適用於 VM 的 Azure 監視器中發生的變更。 

## <a name="updates-for-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器的更新

我們發行了新版本的適用於 VM 的 Azure 監視器。 啟用適用于 Vm 的 Azure 監視器的客戶現在會收到新版本，但已使用適用於 VM 的 Azure 監視器的現有客戶將會收到升級的提示。 如果您在多個工作區中有大型部署，此常見問題和我們的檔會提供執行大規模升級的指引。

透過這項升級，適用於 VM 的 Azure 監視器效能資料會儲存在與[容器 Azure 監視器](container-insights-overview.md)相同的*InsightsMetrics*資料表中，讓您更輕鬆地查詢這兩個資料集。 此外，您也可以儲存我們無法在先前使用的資料表中儲存的更多樣化資料集。 

在下一周或兩個中，我們的效能觀點也會更新為使用這個新的資料表。

我們發現，詢問現有客戶的升級會導致工作流程中斷，這就是為什麼我們在公開預覽期間選擇立即執行此動作，而不是在 GA 之後才進行。


## <a name="what-is-changing"></a>變更內容為何？

我們發行了名為 VMInsights 的新解決方案，其中包含資料收集的額外功能，以及將此資料儲存在 Log Analytics 工作區中的新位置。 

在過去，我們已在您的工作區上啟用 ServiceMap 解決方案，並在您的 Log Analytics 工作區中設定效能計數器，以將資料傳送至*Perf*資料表。 這個新的解決方案會將資料傳送至名為*InsightsMetrics*的資料表，也就是用於容器的 Azure 監視器。 這個資料表架構可讓我們儲存與*Perf*資料表格式不相容的其他計量和服務資料集。


## <a name="how-do-i-upgrade"></a>如何? 升級嗎？
當 Log Analytics 工作區升級至 Vm 的最新版本 Azure 監視器時，它會升級附加至該工作區的每個 Vm 上的相依性代理程式。 需要升級的每個 VM 都將在 Azure 入口網站中適用於 VM 的 Azure 監視器的 [**開始**使用] 索引標籤中識別。 當您選擇升級 VM 時，它會升級該 VM 的工作區，以及附加至該工作區的其他任何 Vm。 您可以選取單一 VM 或多個 Vm、資源群組或訂用帳戶。 

使用下列命令來使用 PowerShell 升級工作區：

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>如果我安裝 VMInsights 解決方案，我應該如何處理 [我的工作區] 中的效能計數器？

目前啟用適用於 VM 的 Azure 監視器的方法會使用您工作區中的效能計數器。 新的方法會將這項資料儲存在名為 `InsightsMetrics`的新資料表中。

當我們更新使用者介面以使用 `InsightsMetrics` 資料表中的資料之後，我們將會更新檔，並透過多個管道來傳達此公告，包括在 Azure 入口網站中顯示橫幅。 此時，如果您不再需要使用這些[效能計數器](vminsights-enable-overview.md#performance-counters-enabled)，您可以選擇在工作區中停用它們。 

>[!NOTE]
>如果您有在 `Perf` 資料表中參考這些計數器的警示規則，您必須更新它們，以參考 `InsightsMetrics` 資料表中所儲存的新資料。 請參閱我們的檔，以取得您可以用來參考此資料表的範例記錄查詢。
>

如果您決定讓效能計數器保持啟用狀態，系統就會根據 [Log Analytics 定價 [（ https://azure.microsoft.com/pricing/details/monitor/)，向您收取資料內嵌並儲存在 `Perf` 資料表中的費用。

## <a name="how-will-this-change-affect-my-alert-rules"></a>這種變更會如何影響我的警示規則？

如果您已建立[記錄警示](../platform/alerts-unified-log.md)來查詢以工作區中啟用之效能計數器為目標的 `Perf` 資料表，您應該更新這些規則，改為參考 `InsightsMetrics` 資料表。 本指南也適用于使用 `ServiceMapComputer_CL` 和 `ServiceMapProcess_CL`的任何記錄搜尋規則，因為這些資料集會移至 `VMComputer` 和 `VMProcess` 資料表。

我們將更新此常見問題和我們的檔，以包含我們所收集之資料集的範例記錄搜尋警示規則。

## <a name="how-will-this-affect-my-bill"></a>這對我的帳單有何影響？

計費仍然是以資料內嵌為基礎，並保留在您的 Log Analytics 工作區中。

我們所收集的機器層級效能資料是相同的，其大小與我們儲存在 `Perf` 資料表中的資料相似，而且會以大約相同的金額計算成本。

## <a name="what-if-i-only-want-to-use-service-map"></a>如果我只想要使用服務對應，該怎麼做？

沒問題。 您會在觀看即將進行之更新的適用於 VM 的 Azure 監視器時，看到 Azure 入口網站中的提示。 發行後，您會收到要求您更新至新版本的提示。 如果您只想要使用[Maps](vminsights-maps.md)功能，可以選擇不要升級並繼續使用適用於 VM 的 Azure 監視器中的 Maps 功能，以及從您的工作區或儀表板磚存取的服務對應解決方案。

如果您選擇在工作區中手動啟用效能計數器，則可以在從 Azure 監視器查看的一些效能圖表中看到資料。 發行新的解決方案之後，我們會更新效能圖表來查詢儲存在 `InsightsMetrics` 資料表中的資料。 如果您想要在這些圖表中查看來自該資料表的資料，您將需要升級至新版的適用於 VM 的 Azure 監視器。

將資料從 `ServiceMapComputer_CL` 和 `ServiceMapProcess_CL` 移動的變更會同時影響服務對應和適用於 VM 的 Azure 監視器，因此您仍然需要規劃這項更新。

如果您選擇不升級至**VMInsights**解決方案，我們會繼續提供舊版的效能活頁簿，以參考 `Perf` 資料表中的資料。  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>服務對應資料集是否也會儲存在 InsightsMetrics 中？

如果您同時使用這兩種方案，資料集會不會重複。 這兩個供應專案都會共用將儲存在 `VMComputer` （先前稱為 ServiceMapComputer_CL）、`VMProcess` （先前稱為 ServiceMapProcess_CL）、`VMConnection`和 `VMBoundPort` 資料表中的資料集，以儲存我們所收集的對應資料集。  

`InsightsMetrics` 資料表會儲存我們所收集的 VM、進程和服務資料集，而且只有在您使用適用於 VM 的 Azure 監視器和 VM Insights 解決方案時，才會填入。 服務對應方案不會收集或或儲存 `InsightsMetrics` 資料表中的資料。

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>如果我的工作區中有服務對應和 VMInsights 解決方案，是否要向我收取雙重費用？

否，這兩個解決方案會共用我們儲存在 `VMComputer` （之前稱為 ServiceMapComputer_CL）、`VMProcess` （先前稱為 ServiceMapProcess_CL）、`VMConnection`和 `VMBoundPort`的對應資料集。 如果您的工作區中有這兩個解決方案，則不會對此資料有雙重費用。

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>如果我移除服務對應或 VMInsights 解決方案，將會移除我的資料嗎？

否，這兩個解決方案會共用我們儲存在 `VMComputer` （之前稱為 ServiceMapComputer_CL）、`VMProcess` （先前稱為 ServiceMapProcess_CL）、`VMConnection`和 `VMBoundPort`的對應資料集。 如果您移除其中一個解決方案，這些資料集會發現仍有使用該資料的解決方案，而且它會保留在 Log Analytics 工作區中。 您必須從工作區中移除這兩個解決方案，才能將資料從其中移除。

## <a name="when-will-this-update-be-released"></a>何時會發行此更新？

我們預計會在2020年1月年初發行適用於 VM 的 Azure 監視器的更新。 當我們更接近1月的發行日期時，我們會在此發佈更新，並在您開啟 Azure 監視器時，于 Azure 入口網站中顯示通知。

## <a name="health-feature-is-in-limited-public-preview"></a>健全狀況功能處於有限的公開預覽狀態

我們已收到客戶關於 VM 健全狀況功能集的許多絕佳意見反應。 這項功能和令人興奮的支援監視工作流程，這方面有許多興趣。 我們打算進行一系列的變更，以新增功能並解決我們所收到的意見反應。 

為了將這些變更對新客戶的影響降至最低，我們已將這項功能移至**有限的公開預覽版**。 此更新會在2019年10月發生。

在適用於 VM 的 Azure 監視器正式運作後，我們打算在2020中重新開機此健康情況功能。

## <a name="how-do-existing-customers-access-the-health-feature"></a>現有客戶如何存取健全狀況功能？

使用健全狀況功能的現有客戶將可繼續存取它，但不會提供給新客戶。  

若要存取此功能，您可以將下列功能旗標 `feature.vmhealth=true` 新增至 Azure 入口網站 URL [https://portal.azure.com](https://portal.azure.com)。 範例 `https://portal.azure.com/?feature.vmhealth=true`。

您也可以使用這個簡短的 url，這會自動設定功能旗標： [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)。

身為現有的客戶，您可以繼續在連線到現有工作區設定的 Vm 上使用健康狀態功能。  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>我現在使用 VM 健全狀況搭配一個環境，並想要將它部署到新的環境

如果您是使用健康情況功能的現有客戶，而且想要將它用於新的推出，請在 vminsights@microsoft.com 洽詢以要求指示。

## <a name="next-steps"></a>後續步驟

若要了解有助於您監視虛擬機器的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。
