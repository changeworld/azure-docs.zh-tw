---
title: Azure 診斷延伸模組總覽
description: 使用 Azure 診斷來在雲端服務、虛擬機器及 Service Fabric 中進行偵錯、測量效能、監視、流量分析等。
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: d9db4b4c8e6d82f29d227b9f8afe528e000c651e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467989"
---
# <a name="azure-diagnostics-extension-overview"></a>Azure 診斷延伸模組總覽
Azure 診斷延伸模組是[Azure 監視器中的代理程式](agents-overview.md)，可從 Azure 計算資源（包括虛擬機器）的客體作業系統收集監視資料。 本文概述 Azure 診斷延伸模組，包括支援的特定功能，以及安裝和設定的選項。 

> [!NOTE]
> Azure 診斷延伸模組是可用來從計算資源的客體作業系統收集監視資料的其中一個代理程式。 如需不同代理程式的說明，以及針對您的需求選取適當的代理程式的指引，請參閱[Azure 監視器代理](agents-overview.md)程式的總覽。

## <a name="comparison-to-log-analytics-agent"></a>與 Log Analytics 代理程式的比較
Azure 監視器中的 Log Analytics 代理程式也可以用來從虛擬機器的客體作業系統收集監視資料。 視您的需求而定，您可以選擇使用其中一種或兩種方式。 如需 Azure 監視器代理程式的詳細比較，請參閱[Azure 監視器代理](agents-overview.md)程式的總覽。 

需要考慮的主要差異如下：

- Azure 診斷延伸模組只能與 Azure 虛擬機器搭配使用。 Log Analytics 代理程式可與 Azure、其他雲端和內部部署中的虛擬機器搭配使用。
- Azure 診斷延伸模組會將資料傳送至 Azure 儲存體、 [Azure 監視器計量](data-platform-metrics.md)（僅限 Windows）和事件中樞。 Log Analytics 代理程式會收集資料以[Azure 監視器記錄](data-platform-logs.md)。
- [解決方案](../monitor-reference.md#insights-and-core-solutions)、[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)和其他服務（例如[Azure 資訊安全中心](/azure/security-center/)）都需要 Log Analytics 代理程式。

## <a name="costs"></a>費用
Azure 診斷擴充功能沒有成本，但您可能會產生資料內嵌的費用。 針對您要收集資料的目的地，檢查[Azure 監視器的定價](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="data-collected"></a>收集的資料
下表列出 Windows 和 Linux 診斷擴充功能可收集的資料。

### <a name="windows-diagnostics-extension-wad"></a>Windows 診斷擴充功能（WAD）

| 資料來源 | 描述 |
| --- | --- |
| Windows 事件記錄   | Windows 事件記錄檔中的事件。 |
| 效能計數器 | 數值，測量作業系統和工作負載不同層面的效能。 |
| IIS 記錄             | 在客體作業系統上執行之 IIS 網站的使用方式資訊。 |
| 應用程式記錄     | 追蹤您的應用程式所撰寫的訊息。 |
| .NET EventSource 記錄 |使用 .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 類別的程式碼編寫事件 |
| [以資訊清單為基礎的 ETW 記錄](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) \(英文\) |任何進程所產生之 Windows 事件的事件追蹤。 |
| 損毀傾印 (記錄)   | 應用程式損毀時的進程狀態相關資訊。 |
| 以檔案為基礎的記錄檔    | 您的應用程式或服務所建立的記錄。 |
| 代理程式診斷記錄 | Azure 診斷本身的相關資訊。 |


### <a name="linux-diagnostics-extension-lad"></a>Linux 診斷擴充功能（LAD）

| 資料來源 | 描述 |
| --- | --- |
| syslog | 傳送至 Linux 事件記錄系統的事件。   |
| 效能計數器  | 數值，測量作業系統和工作負載不同層面的效能。 |
| 記錄檔 | 傳送至以檔案為基礎之記錄檔的專案。  |

## <a name="data-destinations"></a>資料目的地
適用于 Windows 和 Linux 的 Azure 診斷延伸模組一律會將資料收集到 Azure 儲存體帳戶中。 請參閱[安裝和設定 Windows Azure 診斷擴充功能（WAD）](diagnostics-extension-windows-install.md)和[使用 Linux 診斷擴充功能來監視計量和記錄](../../virtual-machines/extensions/diagnostics-linux.md)，以取得收集此資料的特定資料表和 blob 清單。

設定一或多個*資料接收*，將資料傳送至其他目的地。 下列各節列出適用于 Windows 和 Linux 診斷擴充功能的接收。

### <a name="windows-diagnostics-extension-wad"></a>Windows 診斷擴充功能（WAD）

| Destination | 描述 |
|:---|:---|
| Azure 監視器計量 | 收集效能資料以 Azure 監視器計量。 請參閱[將客體作業系統計量傳送至 Azure 監視器度量資料庫](collect-custom-metrics-guestos-resource-manager-vm.md)。  |
| 事件中樞 | 使用 Azure 事件中樞在 Azure 外部傳送資料。 請參閱[串流 Azure 診斷資料至事件中樞](diagnostics-extension-stream-event-hubs.md) |
| Azure 儲存體 Blob | 除了資料表之外，也會在 Azure 儲存體中將資料寫入至 blob。 |
| Application Insights | 從 VM 中執行的應用程式收集資料，以 Application Insights 與其他應用程式監視整合。 請參閱[將診斷資料傳送至 Application Insights](diagnostics-extension-to-application-insights.md)。 |

您也可以將儲存體中的 WAD 資料收集到 Log Analytics 工作區，以使用 Azure 監視器記錄來分析它，雖然 Log Analytics 代理程式通常用於這種功能。 它可以直接將資料傳送至 Log Analytics 工作區，並支援提供額外功能的解決方案和深入解析。  請參閱[從 Azure 儲存體收集 Azure 診斷記錄](diagnostics-extension-logs.md)。 


### <a name="linux-diagnostics-extension-lad"></a>Linux 診斷擴充功能（LAD）
LAD 會將資料寫入 Azure 儲存體中的資料表。 它支援下表中的接收。

| Destination | 描述 |
|:---|:---|
| 事件中樞 | 使用 Azure 事件中樞在 Azure 外部傳送資料。 |
| Azure 儲存體 Blob | 除了資料表之外，也會在 Azure 儲存體中將資料寫入至 blob。 |
| Azure 監視器計量 | 除了 LAD 以外，請安裝 Telegraf 代理程式。 請參閱[使用 InfluxData Telegraf 代理程式收集 LINUX VM 的自訂計量](collect-custom-metrics-linux-telegraf.md)。


## <a name="installation-and-configuration"></a>安裝和組態
診斷延伸模組會實作為 Azure 中的[虛擬機器擴充](/virtual-machines/extensions/overview)功能，因此它支援使用 Resource Manager 範本、POWERSHELL 和 CLI 的相同安裝選項。 如需安裝和維護虛擬機器擴充功能的一般詳細資訊，請參閱[Windows 的虛擬機器擴充功能](/virtual-machines/extensions/features-windows)和[適用于 Linux 的虛擬機器延伸模組和功能](/virtual-machines/extensions/features-linux)。

您也可以在虛擬機器功能表的 **監視** 區段中，于 **診斷設定** 底下的 Azure 入口網站中安裝和設定 Windows 和 Linux 診斷擴充功能。

如需安裝和設定適用于 Windows 和 Linux 的診斷擴充功能的詳細資訊，請參閱下列文章。

- [安裝和設定 Windows Azure 診斷擴充功能（WAD）](diagnostics-extension-windows-install.md)
- [使用 Linux 診斷擴充功能來監視計量和記錄](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>其他檔

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure 雲端服務（傳統） Web 和背景工作角色
- [雲端服務監視簡介](../../cloud-services/cloud-services-how-to-monitor.md)
- [在 Azure 雲端服務中啟用 Azure 診斷](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [適用于 Azure 雲端服務的 Application Insights](../app/cloudservices.md)<br>[使用 Azure 診斷追蹤雲端服務應用程式的流程](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [監視和診斷本機開發設定中的服務](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>後續步驟


* 了解如何[在 Azure 診斷中使用效能計數器](../../cloud-services/diagnostics-performance-counters.md)。
* 如果您在開始診斷，或是在 Azure 儲存體資料表中尋找資料時遇到問題，請參閱[針對 Azure 診斷進行疑難排解](diagnostics-extension-troubleshooting.md)

