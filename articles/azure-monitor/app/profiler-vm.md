---
title: 分析 Azure VM 上的 web 應用程式-Application Insights Profiler
description: 使用 Application Insights Profiler 來分析 Azure VM 上的 Web 應用程式。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f514dd7b54ac091535aeab43a8a7d2a645b50a09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87315796"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>使用 Application Insights Profiler 來分析 Azure 虛擬機器或虛擬機器擴展集上所執行的 Web 應用程式

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您也可以在這些服務上部署 Azure Application Insights Profiler：
* [Azure App Service](./profiler.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Azure 雲端服務](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>在虛擬機器或虛擬機器擴展集上部署 Profiler
本文示範如何在 Azure 虛擬機器 (VM) 或 Azure 虛擬機器擴展集上執行 Application Insights Profiler。 Profiler 會與 VM 的 Azure 診斷擴充功能一起安裝。 設定擴充功能以執行 Profiler，並將 Application Insights SDK 建置到您的應用程式中。

1. 將 Application Insights SDK 新增至您的 [ASP.NET 應用程式](./asp-net.md)。

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

   下列 PowerShell 命令是僅接觸 Azure 診斷擴充功能之現有虛擬機器的替代方法。 將先前提及的 ProfilerSink 新增至 Get-AzVMDiagnosticsExtension 命令所傳回的設定。 然後將更新的設定傳遞至 Set-AzVMDiagnosticsExtension 命令。

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 如果預期的應用程式是透過 [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 執行，則啟用 `IIS Http Tracing` Windows 功能。

   a. 建立環境的遠端存取，然後使用 [新增 Windows 功能](/iis/configuration/system.webserver/tracing/) 視窗。 或在 PowerShell 中執行下列命令 (以系統管理員身分)：  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 如果建立遠端存取發生問題，您可以使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 來執行下列命令：  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 部署應用程式。

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>使用 Azure 資源總管設定 Profiler 接收
我們還沒有方法可以從入口網站設定 Application Insights Profiler 的接收。 您可以使用 Azure 資源總管來設定接收，而不是使用上述的 powershell。 但請注意，如果您再次部署 VM，則接收將會遺失。 您必須更新您在部署 VM 時使用的設定，以保留此設定。

1. 藉由查看為您虛擬機器所安裝的延伸模組，檢查是否已安裝 Windows Azure 診斷延伸模組。  

    ![檢查是否已安裝 WAD 延伸模組][wadextension]

2. 尋找 VM 的 VM 診斷擴充功能。 前往 [https://resources.azure.com](https://resources.azure.com)。 展開您的資源群組、[Microsoft. 計算 virtualMachines]、[虛擬機器名稱] 和 [擴充功能]。  

    ![流覽至 Azure 資源總管中的 WAD config][azureresourceexplorer]

3. 將 Application Insights Profiler 接收新增至 Diagnostics.wadcfg 底下的 SinksConfig 節點。 如果您還沒有 SinksConfig 區段，您可能需要新增一個。 請務必在您的設定中指定適當的 Application Insights iKey。 您必須將瀏覽器模式切換至右上角的 [讀取/寫入]，然後按下藍色的 [編輯] 按鈕。

    ![新增 Application Insights Profiler 接收][resourceexplorersinksconfig]

4. 當您完成編輯設定之後，請按 [Put （Put）]。 如果 put 成功，畫面中間將會出現綠色的核取記號。

    ![傳送 put 要求以套用變更][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>是否可在內部部署伺服器上執行 Profiler？
我們沒有計畫針對內部部署伺服器支援 Applciation Insights Profiler。

## <a name="next-steps"></a>接下來的步驟

- 產生應用程式的流量 (例如，啟動[可用性測試](monitor-web-app-availability.md))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
- 請參閱 Azure 入口網站中的 [Profiler 追蹤](profiler-overview.md?toc=/azure/azure-monitor/toc.json) 。
- 如需 Profiler 問題的疑難排解說明，請參閱 [Profiler 疑難排解](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png

