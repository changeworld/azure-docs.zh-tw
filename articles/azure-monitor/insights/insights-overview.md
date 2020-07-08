---
title: Azure 監視器中的深入解析概觀 | Microsoft Docs
description: 深入解析可為特定應用程式和服務提供 Azure 監視器中自訂的監視體驗。 本文將針對目前可用的每個深入解析提供簡短描述。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: ecd66500fdf968a773d3bc4f3d93363bfe86c763
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836034"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure 監視器中的深入解析概觀
深入解析可為特定應用程式和服務提供自訂的監視體驗。 資料將會儲存在 [Azure 監視器資料平台](../platform/data-platform.md)中，並利用其他 Azure 監視器功能進行分析和警示，但可能會收集其他資料，並在 Azure 入口網站中提供獨特的使用者體驗。 從 Azure 入口網站中 [Azure 監視器] 功能表的 [深入解析] 區段存取深入解析。


下列各節將針對 Azure 監視器中目前可用的深入解析提供簡短描述。 如需每個深入解析的詳細資訊，請參閱更詳細的文件。

## <a name="application-insights"></a>Application Insights
Application Insights 是多個平台上的 Web 開發人員所適用的可延伸「應用程式效能管理」(APM) 服務。 您可以使用它來監視即時 Web 應用程式。 其適用於各種不同平台上的應用程式，包括裝載在內部部署、混合式或任何公用雲端的 .NET、Node.js 和 Java EE。 也可以與您的 DevOps 程序整合，並有各種開發工具的連接點。

請參閱[什麼是 Application Insights？](../app/app-insights-overview.md)。

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>適用於容器的 Azure 監視器
適用於容器的 Azure 監視器會監視部署至 Azure Container Instances 或 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 監視容器很重要，尤其在您使用多個應用程式大規模執行生產環境叢集時。

請參閱[適用於容器的 Azure 監視器概觀](../insights/container-insights-overview.md)。

![適用於容器的 Azure 監視器](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>適用於資源群組的 Azure 監視器 (預覽)
適用於資源群組的 Azure 監視器能夠將個別資源碰到的任何問題加以分級與診斷，同時就資源群組整體的健康狀態與效能提供內容。

請參閱[使用 Azure 監視器監視資源群組 (預覽)](../insights/resource-group-insights.md)。

![適用於資源群組的 Azure 監視器](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>適用於 VM 的 Azure 監視器 (預覽)
適用於 VM 的 Azure 監視器會大規模監視您的 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，並且在其他資源和外部處理序上監視其處理序及相依性。

請參閱[什麼是適用於 VM 的 Azure 監視器？](vminsights-overview.md)

![適用於 VM 的 Azure 監視器](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>適用於網路的 Azure 監視器 (預覽)
[適用於網路的 Azure 監視器](network-insights-overview.md)可針對您所有的網路資源，提供健康情況和計量的全面性檢視。 進階搜尋功能可協助您識別資源相依性，藉由直接搜尋您的網站名稱，來實現不同案例，例如識別裝載網站的資源。

![適用於網路的 Azure 監視器](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>後續步驟
* 深入了解深入解析所用的 [Azure 監視器資料平台](../platform/data-platform.md)。
* 了解 [Azure 監視器所使用的不同資料來源](../platform/data-sources.md)，以及每個深入解析所收集的不同資料類型。
