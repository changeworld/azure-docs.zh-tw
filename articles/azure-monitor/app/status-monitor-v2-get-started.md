---
title: Azure 應用程式 Insights 代理程式-開始使用 |Microsoft Docs
description: Application Insights 代理程式的快速入門手冊。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 4cfa136585611e81a4060c5544d5dc464b32f12c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537435"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>開始使用適用于內部部署伺服器 Azure 監視器 Application Insights 代理程式

本文包含預期適用于大部分環境的快速入門命令。
這些指示取決於散發更新的 PowerShell 資源庫。
這些命令支援 PowerShell `-Proxy`參數。

如需這些命令的說明、自訂指示和疑難排解的相關資訊，請參閱[詳細指示](status-monitor-v2-detailed-instructions.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="download-and-install-via-powershell-gallery"></a>透過 PowerShell 資源庫下載並安裝

### <a name="install-prerequisites"></a>安裝必要條件
以系統管理員身分執行 PowerShell。
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
關閉 PowerShell。

### <a name="install-application-insights-agent"></a>安裝 Application Insights 代理程式
以系統管理員身分執行 PowerShell。
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>啟用監視
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>手動下載並安裝（離線選項）
### <a name="download-the-module"></a>下載模組
從[PowerShell 資源庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)手動下載最新版本的模組。

### <a name="unzip-and-install-application-insights-agent"></a>解壓縮並安裝 Application Insights 代理程式
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>啟用監視
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>後續步驟

 檢視遙測：

- [探索計量](../../azure-monitor/platform/metrics-charts.md)以監視效能和使用量。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- [流量分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。

 新增更多遙測：

- [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)，以查看來自網頁程式碼的例外狀況，並啟用追蹤呼叫。
- [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)，讓您可以插入追蹤和記錄呼叫。

使用 Application Insights 代理程式執行更多工具：

- 如需相關命令的說明，請參閱這裡的[詳細指示](status-monitor-v2-detailed-instructions.md)。
- 使用我們的指南來[疑難排解](status-monitor-v2-troubleshoot.md)Application Insights 代理程式。
