---
title: Azure 診斷擴展概述
description: 使用 Azure 診斷來在雲端服務、虛擬機器及 Service Fabric 中進行偵錯、測量效能、監視、流量分析等。
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672373"
---
# <a name="azure-diagnostics-extension-overview"></a>Azure 診斷擴展概述
Azure 診斷擴展是[Azure 監視器中的代理](agents-overview.md)，用於從 Azure 計算資源（包括虛擬機器）的客體作業系統收集監視資料。 本文概述了 Azure 診斷擴展，包括它支援的特定功能以及安裝和配置選項。 

> [!NOTE]
> Azure 診斷擴展是可用於從計算資源的客體作業系統收集監視資料的代理之一。 有關不同代理的說明以及選擇適合需求的代理的指導，請參閱[Azure 監視器代理概述](agents-overview.md)。

## <a name="comparison-to-log-analytics-agent"></a>與日誌分析代理的比較
Azure 監視器中的日誌分析代理還可用於從虛擬機器的客體作業系統收集監視資料。 根據您的要求，您可以選擇使用任一或兩者。 有關 Azure 監視器代理的詳細比較，請參閱[Azure 監視器代理的概述](agents-overview.md)。 

需要考慮的主要區別是：

- Azure 診斷擴展只能與 Azure 虛擬機器一起使用。 日誌分析代理可用於 Azure 中的虛擬機器、其他雲和本地。
- Azure 診斷擴展將資料發送到 Azure 存儲[、Azure 監視器指標](data-platform-metrics.md)（僅限 Windows）和事件中心。 日誌分析代理將資料收集到 Azure[監視器日誌](data-platform-logs.md)。
- [解決方案](../monitor-reference.md#insights-and-core-solutions)[、VM 的 Azure 監視器](../insights/vminsights-overview.md)和其他服務（如[Azure 安全中心](/azure/security-center/)）需要日誌分析代理。

## <a name="costs"></a>費用
Azure 診斷擴展不收取任何費用，但可能會對引入的資料產生費用。 檢查要收集資料的目標的[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="data-collected"></a>收集的資料
下表列出了 Windows 和 Linux 診斷擴展可以收集的資料。

### <a name="windows-diagnostics-extension-wad"></a>視窗診斷擴展 （WAD）

| 資料來源 | 描述 |
| --- | --- |
| Windows 事件記錄   | 來自 Windows 事件日誌的事件。 |
| 效能計數器 | 測量作業系統和工作負載不同方面性能的數值。 |
| IIS 記錄             | 在客體作業系統上運行的 IIS 網站的使用方式資訊。 |
| 應用程式記錄     | 跟蹤應用程式寫入的消息。 |
| .NET EventSource 記錄 |使用 .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 類別的程式碼編寫事件 |
| [以資訊清單為基礎的 ETW 記錄](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) \(英文\) |任何進程生成的 Windows 事件的事件跟蹤。 |
| 損毀傾印 (記錄)   | 有關應用程式崩潰時進程狀態的資訊。 |
| 基於檔的日誌    | 您的應用程式或服務所建立的記錄。 |
| 代理診斷日誌 | 有關 Azure 診斷本身的資訊。 |


### <a name="linux-diagnostics-extension-lad"></a>Linux 診斷擴展 （LAD）

| 資料來源 | 描述 |
| --- | --- |
| syslog | 發送到 Linux 事件日誌記錄系統的事件。   |
| 效能計數器  | 測量作業系統和工作負載不同方面性能的數值。 |
| 記錄檔 | 發送到基於檔的日誌的條目。  |

## <a name="data-destinations"></a>資料目的地
Windows 和 Linux 的 Azure 診斷擴展始終將資料收集到 Azure 存儲帳戶中。 請參閱[安裝和配置 Windows Azure 診斷擴展 （WAD）](diagnostics-extension-windows-install.md)和使用[Linux 診斷擴展來監視](../../virtual-machines/extensions/diagnostics-linux.md)收集此資料的特定表和 blob 的清單的指標和日誌。

配置一個或多個*資料接收器*以將資料發送到其他其他目標。 以下部分列出了可用於 Windows 和 Linux 診斷擴展的接收器。

### <a name="windows-diagnostics-extension-wad"></a>視窗診斷擴展 （WAD）

| Destination | 描述 |
|:---|:---|
| Azure 監視器指標 | 將效能資料收集到 Azure 監視器指標。 請參閱[將來賓 OS 指標發送到 Azure 監視器指標資料庫](collect-custom-metrics-guestos-resource-manager-vm.md)。  |
| 事件中樞 | 使用 Azure 事件中心在 Azure 外部發送資料。 請參閱[將 Azure 診斷資料流程式傳輸到事件中心](diagnostics-extension-stream-event-hubs.md) |
| Azure 儲存體 Blob | 除了表之外，將資料寫入 Azure 存儲中的 Blob。 |
| Application Insights | 從 VM 中運行的應用程式收集資料到應用程式見解，以便與其他應用程式監視集成。 請參閱[將診斷資料發送到應用程式見解](diagnostics-extension-to-application-insights.md)。 |

還可以將 WAD 資料從存儲收集到日誌分析工作區中，以便使用 Azure 監視器日誌進行分析，儘管日誌分析代理通常用於此功能。 它可以將資料直接發送到日誌分析工作區，並支援提供其他功能的解決方案和見解。  請參閱[從 Azure 存儲收集 Azure 診斷日誌](diagnostics-extension-logs.md)。 


### <a name="linux-diagnostics-extension-lad"></a>Linux 診斷擴展 （LAD）
LAD 將資料寫入 Azure 存儲中的表。 它支援下表中的接收器。

| Destination | 描述 |
|:---|:---|
| 事件中樞 | 使用 Azure 事件中心在 Azure 外部發送資料。 |
| Azure 儲存體 Blob | 除了表之外，將資料寫入 Azure 存儲中的 Blob。 |
| Azure 監視器指標 | 除 LAD 外，還安裝 Telegraf 代理。 請參閱[收集Linux VM的自訂指標與流入資料遠端格拉夫代理](collect-custom-metrics-linux-telegraf.md)。


## <a name="installation-and-configuration"></a>安裝和組態
診斷擴展在 Azure 中作為[虛擬機器擴展](../../virtual-machines/extensions/overview.md)實現，因此它支援使用資源管理器範本、PowerShell 和 CLI 的相同安裝選項。 有關安裝和維護虛擬機器擴展的一般詳細資訊，請參閱 Windows 和[虛擬機器擴展和 Linux](../../virtual-machines/extensions/features-linux.md)的[虛擬機器擴展和](../../virtual-machines/extensions/features-windows.md)功能。

您還可以在虛擬機器功能表的 **"監視"** 部分下的 **"診斷設置**"下，在 Azure 門戶中安裝和配置 Windows 和 Linux 診斷擴展。

有關安裝和配置 Windows 和 Linux 的診斷擴展的詳細資訊，請參閱以下文章。

- [安裝和配置 Windows Azure 診斷擴展 （WAD）](diagnostics-extension-windows-install.md)
- [使用 Linux 診斷擴充功能監視計量與記錄](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>其他文檔

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure 雲服務（經典）Web 和輔助角色
- [雲端服務監視簡介](../../cloud-services/cloud-services-how-to-monitor.md)
- [在 Azure 雲端服務中啟用 Azure 診斷](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Azure 雲服務的應用程式見解](../app/cloudservices.md)<br>[使用 Azure 診斷追蹤雲端服務應用程式的流程](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [監視和診斷本機開發設定中的服務](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>後續步驟


* 了解如何[在 Azure 診斷中使用效能計數器](../../cloud-services/diagnostics-performance-counters.md)。
* 如果在 Azure 存儲表中啟動或查找資料時遇到診斷問題，請參閱["故障拍攝 Azure 診斷"](diagnostics-extension-troubleshooting.md)

