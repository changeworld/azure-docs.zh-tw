---
title: Azure 應用程式見解代理概述 |微軟文檔
description: 應用程式見解代理概述。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4a240bc62816a46bc37108777a8b081b74047738
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275707"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>為本機伺服器部署 Azure 監視器應用程式見解代理

> [!IMPORTANT]
> 本指南建議用於應用程式見解代理的本地和非 Azure 雲部署。 以下是[Azure 虛擬機器和虛擬機器縮放集部署的建議](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)方法。

應用程式見解代理（以前稱為狀態監視器 V2）是發佈到[PowerShell 庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)的 PowerShell 模組。
它替換[狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)。
遙測將發送到 Azure 門戶，您可以在其中[監視](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)應用。

> [!NOTE]
> 該模組目前僅支援使用 IIS 託管的 .NET Web 應用的無代碼檢測。 使用 SDK 檢測核心、JAVA 和 Node.js 應用程式ASP.NET。

## <a name="powershell-gallery"></a>PowerShell 資源庫

應用程式見解代理位於此處： https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell 資源庫](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- 請參閱[入門說明](status-monitor-v2-get-started.md)，以便從簡潔的代碼示例開始。
- 有關如何入門的深入瞭解[，請參閱詳細說明](status-monitor-v2-detailed-instructions.md)。

## <a name="powershell-api-reference"></a>電源殼 API 參考
- [禁用應用程式見解監控](status-monitor-v2-api-disable-monitoring.md)
- [禁用檢測引擎](status-monitor-v2-api-disable-instrumentation-engine.md)
- [啟用應用程式洞察監控](status-monitor-v2-api-enable-monitoring.md)
- [啟用檢測引擎](status-monitor-v2-api-enable-instrumentation-engine.md)
- [獲取應用程式洞察監控配置](status-monitor-v2-api-get-config.md)
- [獲取應用程式見解監視狀態](status-monitor-v2-api-get-status.md)
- [設置應用程式洞察監控配置](status-monitor-v2-api-set-config.md)
- [啟動-應用程式洞察監控跟蹤](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>疑難排解
- [疑難排解](status-monitor-v2-troubleshoot.md)
- [已知問題](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>常見問題集

- 應用程式見解代理是否支援代理安裝？

  *是的*。 下載應用程式見解代理有多種方法。 如果您的電腦具有 Internet 存取權限，則可以使用`-Proxy`參數將板載到 PowerShell 庫。
您還可以手動下載模組，並將其安裝到您的電腦上或直接使用它。
這些選項中的每一個都在[詳細說明中描述](status-monitor-v2-detailed-instructions.md)。

- 狀態監視器 v2 是否支援 ASP.NET核心應用程式？

  *No*。 有關啟用監視ASP.NET核心應用程式的說明，請參閱[ASP.NET核心應用程式的應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)。 無需為ASP.NET核心應用程式安裝 StatusMonitor。 即使 ASP.NET核心應用程式託管在 IIS 中也是如此。

- 如何驗證啟用是否成功？

  - [獲取應用程式見解監視狀態](status-monitor-v2-api-get-status.md)Cmdlet 可用於驗證啟用是否成功。
  - 我們建議您使用[即時指標](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)快速確定應用是否正在發送遙測資料。

  - 您還可以使用[日誌分析](../log-query/get-started-portal.md)列出當前發送遙測的所有雲角色：
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>後續步驟

檢視遙測：

* [流覽指標](../../azure-monitor/app/metrics-explorer.md)以監視性能和使用方式。
* [搜索事件和日誌](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
* 對更高級的查詢[流量分析](../../azure-monitor/app/analytics.md)。
* [創建儀表板](../../azure-monitor/app/overview-dashboard.md)。

新增更多遙測：

* [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
* [添加 Web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁代碼中的異常並啟用跟蹤調用。
* [將應用程式見解 SDK 添加到代碼中](../../azure-monitor/app/asp-net.md)，以便插入跟蹤和日誌調用。

