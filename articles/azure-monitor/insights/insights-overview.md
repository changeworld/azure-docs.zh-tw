---
title: Azure 監視器中的見解概述 |微軟文檔
description: 見解在 Azure 監視器中為特定應用程式和服務提供了自訂的監視體驗。 本文簡要描述了當前可用的每個見解。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657243"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure 監視器中的見解概述
見解為特定應用程式和服務提供定制的監控體驗。 它們將資料存儲在[Azure 監視器資料平臺](../platform/data-platform.md)中，並利用其他 Azure 監視器功能進行分析和警報，但可能會收集其他資料並在 Azure 門戶中提供獨特的使用者體驗。 從 Azure 門戶中的 Azure 監視器功能表的 **"見解"** 部分訪問見解。

以下各節簡要介紹了 Azure 監視器中當前可用的見解。 有關每個文檔的詳細資訊，請參閱詳細文檔。

## <a name="application-insights"></a>Application Insights
Application Insights 是多個平台上的 Web 開發人員所適用的可延伸「應用程式效能管理」(APM) 服務。 您可以使用它來監視即時 Web 應用程式。 它適用于各種平臺上的應用程式，包括 .NET、Node.js 和 JAVA EE，託管在本地、混合雲或任何公共雲。 它還與您的 DevOps 流程集成，並具有與各種開發工具的連接點。

請參閱[什麼是應用程式見解？](../app/app-insights-overview.md)

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>適用於容器的 Azure 監視器
適用於容器的 Azure 監視器會監視部署至 Azure Container Instances 或 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 監視容器很重要，尤其在您使用多個應用程式大規模執行生產環境叢集時。

[有關容器概述，](../insights/container-insights-overview.md)請參閱 Azure 監視器。

![適用於容器的 Azure 監視器](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>資源組的 Azure 監視器（預覽）
資源組的 Azure 監視器有助於對單個資源遇到的任何問題進行會審和診斷，同時提供資源組整體運行狀況和性能的上下文。

請參閱[使用 Azure 監視器（預覽）監視資源組](../insights/resource-group-insights.md)。

![資源組的 Azure 監視器](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>虛擬機器的 Azure 監視器（預覽版）
適用於 VM 的 Azure 監視器會大規模監視您的 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，並且在其他資源和外部處理序上監視其處理序及相依性。

請參閱[什麼是 VM 的 Azure 監視器？](vminsights-overview.md)

![適用於 VM 的 Azure 監視器](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>網路 Azure 監視器（預覽版）
[網路 Azure 監視器](network-insights-overview.md)為所有網路資源提供運行狀況和指標的全面視圖。 高級搜索功能可説明您識別資源依賴項，通過搜索網站名稱，啟用標識託管網站的資源等方案。

![適用於網路的 Azure 監視器](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>後續步驟
* 詳細瞭解由見解利用的[Azure 監視器資料平臺](../platform/data-platform.md)。
* 瞭解 Azure[監視器使用的不同資料來源](../platform/data-sources.md)以及每個見解收集的不同類型的資料。
