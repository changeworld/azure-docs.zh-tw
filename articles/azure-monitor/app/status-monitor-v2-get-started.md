---
title: Azure 應用程式 Insights 代理程式-開始使用 |Microsoft Docs
description: Application Insights 代理程式的快速入門手冊。 無須重新部署網站，即可監視網站效能。 適用于內部部署、Vm 或 Azure 中裝載的 ASP.NET web 應用程式。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 690304ecef80d988f9a554cd10ce4689f5c72133
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070134"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Azure 監視器內部部署伺服器的 Application Insights 代理程式入門

本文包含預期適用于大部分環境的快速入門命令。
這些指示取決於發佈更新的 PowerShell 資源庫。
這些命令支援 PowerShell `-Proxy` 參數。

如需這些命令的說明、自訂指示和疑難排解的相關資訊，請參閱 [詳細的指示](status-monitor-v2-detailed-instructions.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="download-and-install-via-powershell-gallery"></a>下載並安裝 PowerShell 資源庫

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
    
        
## <a name="download-and-install-manually-offline-option"></a>以手動方式下載並安裝 (offline 選項) 
### <a name="download-the-module"></a>下載模組
從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)手動下載模組的最新版本。

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

- [探索計量](../platform/metrics-charts.md) 來監視效能和使用量。
- [搜尋事件和記錄](./diagnostic-search.md) 以診斷問題。
- [流量分析](../log-query/log-query-overview.md) 進行更先進的查詢。
- [建立儀表板](./overview-dashboard.md)。

 新增更多遙測：

- [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [新增 web 用戶端遙測](./javascript.md) 以查看網頁程式碼的例外狀況，並啟用追蹤呼叫。
- [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](./asp-net.md) ，讓您可以插入追蹤和記錄呼叫。

使用 Application Insights 代理程式執行更多作業：

- 請參閱 [詳細的指示](status-monitor-v2-detailed-instructions.md) ，以取得在此找到的命令說明。
- 使用我們的指南對 Application Insights 代理程式 [進行疑難排解](status-monitor-v2-troubleshoot.md) 。

