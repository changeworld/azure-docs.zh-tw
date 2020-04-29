---
title: Azure 監視器中的深入剖析總覽 |Microsoft Docs
description: 深入解析會針對特定應用程式和服務，在 Azure 監視器中提供自訂的監視體驗。 本文提供每個目前可用的深入解析的簡短描述。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657243"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure 監視器中的見解總覽
深入解析針對特定應用程式和服務提供自訂的監視體驗。 它們會將資料儲存在[Azure 監視器資料平臺](../platform/data-platform.md)中，並利用其他 Azure 監視器的功能來進行分析和警示，但可能會收集其他資料，並在 Azure 入口網站中提供獨特的使用者體驗。 從 Azure 入口網站的 [Azure 監視器] 功能表的 [**深入**解析] 區段存取見解。

下列各節提供 Azure 監視器中目前可用之深入解析的簡短描述。 如需每個的詳細資訊，請參閱詳細檔。

## <a name="application-insights"></a>Application Insights
Application Insights 是多個平台上的 Web 開發人員所適用的可延伸「應用程式效能管理」(APM) 服務。 您可以使用它來監視即時 Web 應用程式。 它適用于各種不同平臺上的應用程式，包括裝載于內部部署、混合式或任何公用雲端的 .NET、node.js 和 JAVA EE。 它也會與您的 DevOps 流程整合，並具有各種開發工具的連接點。

請參閱[什麼是 Application Insights？](../app/app-insights-overview.md)。

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>適用於容器的 Azure 監視器
適用於容器的 Azure 監視器會監視部署至 Azure Container Instances 或 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 監視容器很重要，尤其在您使用多個應用程式大規模執行生產環境叢集時。

請參閱[容器的 Azure 監視器總覽](../insights/container-insights-overview.md)。

![適用於容器的 Azure 監視器](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>適用于資源群組的 Azure 監視器（預覽）
資源群組的 Azure 監視器有助於分級和診斷個別資源所遇到的任何問題，同時提供內容給整體資源群組的健全狀況和效能。

請參閱[使用 Azure 監視器監視資源群組（預覽）](../insights/resource-group-insights.md)。

![資源群組的 Azure 監視器](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>適用於 VM 的 Azure 監視器（預覽）
適用於 VM 的 Azure 監視器會大規模監視您的 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，並且在其他資源和外部處理序上監視其處理序及相依性。

請參閱[什麼是適用於 VM 的 Azure 監視器？](vminsights-overview.md)

![適用於 VM 的 Azure 監視器](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>網路的 Azure 監視器（預覽）
[網路的 Azure 監視器](network-insights-overview.md)可讓您全面瞭解所有網路資源的健全狀況和計量。 先進的搜尋功能可協助您識別資源相依性，藉由直接搜尋您的網站名稱，來啟用識別裝載網站的資源等案例。

![適用於網路的 Azure 監視器](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>後續步驟
* 深入瞭解深入解析所利用的[Azure 監視器資料平臺](../platform/data-platform.md)。
* 瞭解 Azure 監視器所使用的不同[資料來源](../platform/data-sources.md)，以及每個深入解析所收集的不同資料類型。
