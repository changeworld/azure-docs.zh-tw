---
title: Azure 應用程式見解代理詳細說明 |微軟文件
description: 有關使用應用程式見解代理入門的詳細說明。 在不重新部署網站的情況下監控網站性能。 與本地、VM 或 Azure 上託管的ASP.NET Web 應用配合使用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2a0eb83b4b056230e8e7ef103f220b216fceb9e9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537452"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>應用程式見解代理 (以前稱為狀態監視器 v2):詳細說明

本文介紹如何登上 PowerShell 庫並下載應用程式監視器模組。
包括入門所需的最常見的參數。
如果您無法上網,我們還提供了手動下載說明。

## <a name="get-an-instrumentation-key"></a>取得偵測金鑰

要開始,您需要一個檢測密鑰。 有關詳細資訊,請參閱[建立應用程式見解資源](create-new-resource.md#copy-the-instrumentation-key)。

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>使用提升的執行原則以管理員身份執行 PowerShell

### <a name="run-as-admin"></a>依管理員識別

PowerShell 需要管理員級別的許可權才能對您的電腦進行更改。
### <a name="execution-policy"></a>執行原則
- 說明:預設情況下,禁用運行 PowerShell 腳本。 我們建議僅允許「當前範圍」的遠端簽名腳本。
- 參考:[關於執行原則](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)與[集執行原則](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)。
- 指令: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- 選擇性參數：
    - `-Force`. 繞過確認提示。

**範例錯誤**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell 的必要條件

透過執行指令稽核 PowerShell`$PSVersionTable`實體 。
此指令產生以下輸出:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

這些說明在運行 Windows 10 的電腦上和上面列出的版本上編寫和測試。

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 函式庫的先決條件

這些步驟將準備伺服器從 PowerShell 庫下載模組。

> [!NOTE] 
> Windows 10、Windows 伺服器 2016 和 PowerShell 6 支援 PowerShell 庫。
> 有關早期版本的資訊,請參閱[安裝 PowerShellGet](/powershell/scripting/gallery/installing-psget)。


1. 使用提升的執行策略以管理員身份運行 PowerShell。
2. 安裝 NuGet 套件提供者。
    - 說明:您需要此提供程式與基於 NuGet 的儲存庫(如 PowerShell 庫)進行交互。
    - 參考:[安裝套件提供者](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - 指令: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - 選用參數：
        - `-Proxy`. 指定請求的代理伺服器。
        - `-Force`. 繞過確認提示。
    
    沒有未設定 NuGet,您將收到此提示:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. 將 PowerShell 庫配置為受信任的儲存庫。
    - 說明:預設情況下,PowerShell 庫是不受信任的存儲庫。
    - 參考: [Set-PS 儲存函式庫](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)。
    - 指令: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - 選擇性參數：
        - `-Proxy`. 指定請求的代理伺服器。

    如果 PowerShell 函式庫不受信任,您將收到此提示:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    您可以透過`Get-PSRepository`執行指令確認此更改並審核所有 PSReposis。

4. 安裝最新版本的 PowerShellGet。
    - 說明:此模組包含用於從 PowerShell 庫獲取其他模組的工具。 版本 1.0.0.1 隨同 Windows 10 和 Windows 伺服器一起提供。 版本 1.6.0 或更高版本是必需的。 要確定安裝了哪個版本,請運行該`Get-Command -Module PowerShellGet`命令。
    - 參考:[安裝電源殼 。](/powershell/scripting/gallery/installing-psget)
    - 指令: `Install-Module -Name PowerShellGet`.
    - 選用參數：
        - `-Proxy`. 指定請求的代理伺服器。
        - `-Force`. 繞過「已安裝」警告並安裝最新版本。

    如果您不使用最新版本的 PowerShellGet,您將收到此錯誤:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. 重新啟動電源外殼。 無法在當前會話中載入新版本。 新的 PowerShell 會話將載入最新版本的 PowerShellGet。

## <a name="download-and-install-the-module-via-powershell-gallery"></a>透過 PowerShell 函式庫下載並安裝模組

這些步驟將從 PowerShell 庫中下載 Az.應用程式監視器模組。

1. 確保滿足 PowerShell 庫的所有先決條件。
2. 使用提升的執行策略以管理員身份運行 PowerShell。
3. 安裝 Az.應用程式監視器模組。
    - 參考:[安裝模組](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - 指令: `Install-Module -Name Az.ApplicationMonitor`.
    - 選用參數：
        - `-Proxy`. 指定請求的代理伺服器。
        - `-AllowPrerelease`. 允許安裝 Alpha 和 Beta 版本。
        - `-AcceptLicense`. 繞過「接受許可證」提示
        - `-Force`. 繞過「不受信任的存儲庫」 警告。

## <a name="download-and-install-the-module-manually-offline-option"></a>手動下載並安裝模組 (離線選項)

如果由於任何原因無法連接到 PowerShell 模組,您可以手動下載並安裝 Az.ApplicationMonitor 模組。

### <a name="manually-download-the-latest-nupkg-file"></a>手動下載最新的 nupkg 檔案

1. 移至 https://www.powershellgallery.com/packages/Az.ApplicationMonitor 。
2. 在**版本歷史記錄表**中選擇檔的最新版本。
3. 在 **「安裝選項**」下,選擇 **「手動下載**」 。

### <a name="option-1-install-into-a-powershell-modules-directory"></a>選項 1: 安裝到 PowerShell 模組目錄中
將手動下載的 PowerShell 模組安裝到 PowerShell 目錄中,以便 PowerShell 作業階段可以發現該模組。
有關詳細資訊,請參閱[安裝電源外殼模組](/powershell/scripting/developer/module/installing-a-powershell-module)。


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>使用延伸檔 (v1.0.1.0) 將 nupkg 解壓縮為 zip 檔案

- 說明:Microsoft.PowerShell.Archive(v1.0.1.0)的基本版本無法解壓縮 nupkg 檔。 使用 .zip 副檔名重新命名檔案。
- 參考:[展開歸檔](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>使用延伸檔 (v1.1.0.0) 解壓 nupkg

- 說明:使用當前版本的"展開存檔"來解壓縮 nupkg 檔,而無需更改擴展名。
- 參考:[延伸檔](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)與[微軟.PowerShell.存檔](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>選項 2:手動解壓和匯入 nupkg
將手動下載的 PowerShell 模組安裝到 PowerShell 目錄中,以便 PowerShell 作業階段可以發現該模組。
有關詳細資訊,請參閱[安裝電源外殼模組](/powershell/scripting/developer/module/installing-a-powershell-module)。

如果要將模組安裝到任何其他目錄中,請使用[Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)手動導入模組。

> [!IMPORTANT] 
> DLL 將通過相對路徑進行安裝。
> 將包的內容存儲在預期的運行時目錄中,並確認訪問許可權允許讀取,但不能寫入。

1. 將擴展更改為".zip",並將包的內容提取到預期的安裝目錄中。
2. 尋找 Az.應用程式監視器.psd1 的檔路徑。
3. 使用提升的執行策略以管理員身份運行 PowerShell。
4. 使用`Import-Module Az.ApplicationMonitor.psd1`命令載入模組。
    

## <a name="route-traffic-through-a-proxy"></a>透過代理路由流量

當您監視專用 Intranet 上的電腦時,您需要透過代理路由 HTTP 流量。

PowerShell 命令從 PowerShell 庫下載和安裝 Az.`-Proxy`應用程式監視器支援 參數。
編寫安裝腳本時,請查看前面的說明。

應用程式見解 SDK 需要將應用的遙測數據發送到 Microsoft。 我們建議您在 Web.config 檔中為應用設定代理設定。 有關詳細資訊,請參閱[應用程式見解常見問題解答:代理傳遞](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)。


## <a name="enable-monitoring"></a>啟用監視

使用`Enable-ApplicationInsightsMonitoring`命令啟用監視。

有關如何使用此 cmdlet 的詳細資訊,請參閱[API 參考](status-monitor-v2-api-enable-monitoring.md)。



## <a name="next-steps"></a>後續步驟

 檢視遙測：

- [流覽指標](../../azure-monitor/platform/metrics-charts.md)以監視性能和使用方式。
- [搜索事件和日誌](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 對更進階查詢[使用分析](../../azure-monitor/app/analytics.md)。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。

 新增更多遙測：

- [建立 Web 測試](monitor-web-app-availability.md)，確定您的網站保持即時狀態。
- [添加 Web 客戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁代碼中的異常並啟用追蹤呼叫。
- [將應用程式見解 SDK 添加到代碼中](../../azure-monitor/app/asp-net.md),以便插入追蹤和紀錄調用。

使用應用程式見解代理執行更多操作:

- 使用我們的指南對應用程式見解代理[程式進行故障排除](status-monitor-v2-troubleshoot.md)。
