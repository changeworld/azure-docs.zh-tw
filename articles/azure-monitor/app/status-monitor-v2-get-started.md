---
title: Azure 應用程式見解代理 - 入門 |微軟文檔
description: 應用程式見解代理的快速入門手冊。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7819de1f3dfab7f934421de86c0481d2e063f7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671183"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>使用本機伺服器的 Azure 監視器應用程式見解代理入門

本文包含預期適用于大多數環境的快速入門命令。
這些說明依賴于 PowerShell 庫來分發更新。
這些命令支援 PowerShell`-Proxy`參數。

有關這些命令、自訂說明和故障排除資訊的說明，請參閱[詳細說明](status-monitor-v2-detailed-instructions.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="download-and-install-via-powershell-gallery"></a>通過 PowerShell 庫下載並安裝

### <a name="install-prerequisites"></a>安裝必要條件
以管理員身份運行 PowerShell。
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
關閉 PowerShell。

### <a name="install-application-insights-agent"></a>安裝應用程式見解代理
以管理員身份運行 PowerShell。
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>啟用監視
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>手動下載和安裝（離線選項）
### <a name="download-the-module"></a>下載模組
手動從[PowerShell 庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)下載最新版本的模組。

### <a name="unzip-and-install-application-insights-agent"></a>解壓縮並安裝應用程式見解代理
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

- [流覽指標](../../azure-monitor/app/metrics-explorer.md)以監視性能和使用方式。
- [搜索事件和日誌](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 對更高級的查詢[流量分析](../../azure-monitor/app/analytics.md)。
- [創建儀表板](../../azure-monitor/app/overview-dashboard.md)。

 新增更多遙測：

- [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [添加 Web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁代碼中的異常並啟用跟蹤調用。
- [將應用程式見解 SDK 添加到代碼中](../../azure-monitor/app/asp-net.md)，以便插入跟蹤和日誌調用。

使用應用程式見解代理執行更多操作：

- 查看[詳細說明](status-monitor-v2-detailed-instructions.md)，瞭解此處找到的命令的說明。
- 使用我們的指南對應用程式見解代理[進行故障排除](status-monitor-v2-troubleshoot.md)。
