---
title: Azure VM 上的設定檔 Web 應用 - 應用程式見解探測器
description: 使用 Application Insights Profiler 來分析 Azure VM 上的 Web 應用程式。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671574"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>使用 Application Insights Profiler 來分析 Azure 虛擬機器或虛擬機器擴展集上所執行的 Web 應用程式

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您也可以在這些服務上部署 Azure Application Insights Profiler：
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲服務](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 服務結構](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>在虛擬機器或虛擬機器擴展集上部署 Profiler
本文示範如何在 Azure 虛擬機器 (VM) 或 Azure 虛擬機器擴展集上執行 Application Insights Profiler。 Profiler 會與 VM 的 Azure 診斷擴充功能一起安裝。 設定擴充功能以執行 Profiler，並將 Application Insights SDK 建置到您的應用程式中。

1. 將應用程式見解 SDK 添加到[ASP.NET應用程式中](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)。

   若要檢視您要求的設定檔，您必須將要求遙測傳送至 Application Insights。

1. 在 VM 上安裝 Azure 診斷擴充功能。 如需完整的 Resource Manager 範本範例，請參閱：  
   * [虛擬機器](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [虛擬機器擴展集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     關鍵部分在於 WadCfg 中的 ApplicationInsightsProfilerSink。 若要讓 Azure 診斷啟用 Profiler 將資料傳送至 iKey，請在此區段新增另一個接收端。
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. 部署修改過的環境部署定義。  

   套用修改通常會牽涉到完整範本部署或透過 PowerShell Cmdlet 或 Visual Studio 的雲端服務型發佈。  

   以下 PowerShell 命令是僅接觸 Azure 診斷擴展的現有虛擬機器的替代方法。 將前面提到的設定檔器到 Get-AzVM 診斷擴展命令返回的配置中。 然後，將更新的配置傳遞給 Set-AzVM 診斷擴展命令。

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 如果預期的應用程式是透過 [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 執行，則啟用 `IIS Http Tracing` Windows 功能。

   a. 建立環境的遠端存取，然後使用 [新增 Windows 功能]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) 視窗。 或在 PowerShell 中執行下列命令 (以系統管理員身分)：  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 如果建立遠端存取發生問題，您可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 來執行下列命令：  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 部署應用程式。

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>使用 Azure 資源資源管理器設置探測器接收器
我們還沒有從門戶設置應用程式見解探測器接收器的方法。 您可以使用 Azure 資源資源管理器設置接收器，而不是使用如上所述的電源殼。 但請注意，如果再次部署 VM，接收器將丟失。 在部署 VM 時，您需要更新使用的配置以保留此設置。

1. 通過查看為虛擬機器安裝的擴展，檢查是否安裝了 Windows Azure 診斷擴展。  

    ![檢查是否安裝了 WAD 擴展][wadextension]

2. 查找 VM 的 VM 診斷擴展。 轉到[https://resources.azure.com](https://resources.azure.com)。 展開資源組，Microsoft.計算虛擬機器、虛擬機器名稱和擴展。  

    ![導航到 Azure 資源資源管理器中的 WAD 配置][azureresourceexplorer]

3. 將應用程式見解探測器接收器添加到 WadCfg 下的 SinksConfig 節點。 如果您還沒有 SinksConfig 節，則可能需要添加一個。 請務必在設置中指定正確的應用程式見解 iKey。 您需要將資源管理器模式切換到右上角的"讀取/寫入"，然後按藍色"編輯"按鈕。

    ![添加應用程式見解探測器接收器][resourceexplorersinksconfig]

4. 編輯完配置後，按"放"。 如果 put 成功，螢幕中間將顯示綠色檢查。

    ![發送提交請求以應用更改][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>是否可在內部部署伺服器上執行 Profiler？
我們沒有計畫針對內部部署伺服器支援 Applciation Insights Profiler。

## <a name="next-steps"></a>後續步驟

- 產生應用程式的流量 (例如，啟動[可用性測試](monitor-web-app-availability.md))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
- 請參閱 Azure 門戶中的[探測器跟蹤](profiler-overview.md?toc=/azure/azure-monitor/toc.json)。
- 如需 Profiler 問題的疑難排解說明，請參閱 [Profiler 疑難排解](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
