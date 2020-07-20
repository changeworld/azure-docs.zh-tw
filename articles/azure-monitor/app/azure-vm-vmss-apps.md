---
title: 監視 Azure Vm 上的效能-Azure 應用程式深入資訊
description: Azure VM 和 Azure 虛擬機器擴展集的應用程式效能監視。 圖表載入和回應時間、相依性資訊，以及設定效能警示。
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77661323"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>在 Azure 虛擬機器和 Azure 虛擬機器擴展集上部署 Azure 監視器 Application Insights 代理程式

現在，在[azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)和[azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)上執行的 .net web 應用程式上啟用監視比以往更容易。 取得使用 Application Insights 而不修改程式碼的所有優點。

本文會逐步引導您使用 Application Insights 代理程式來啟用 Application Insights 監視，並提供將大規模部署程式自動化的初步指引。

> [!IMPORTANT]
> 適用于 .NET 的 Azure 應用程式 Insights 代理程式目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 某些功能可能不受支援，有些可能會有限制的功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enable-application-insights"></a>啟用 Application Insights

有兩種方式可針對 Azure 虛擬機器和 Azure 虛擬機器擴展集託管應用程式啟用應用程式監視：

* 透過 Application Insights 代理程式的**無程式碼**
    * 這個方法最容易啟用，而且不需要任何 advanced 設定。 這通常稱為「執行時間」監視。

    * 針對 Azure 虛擬機器和 Azure 虛擬機器擴展集，我們建議您至少啟用此層級的監視。 之後，根據您的特定案例，您可以評估是否需要手動檢測。

    * Application Insights 代理程式會自動收集與 .NET SDK 相同的預設相依性信號。 若要深入瞭解，請參閱相依性[自動收集](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net)。
        > [!NOTE]
        > 目前僅支援 .Net IIS 裝載的應用程式。 使用 SDK 來檢測裝載于 Azure 虛擬機器和虛擬機器擴展集上的 ASP.NET Core、JAVA 和 Node.js 應用程式。

* 以**程式碼為基礎的**via SDK

    * 這種方法更容易自訂，但需要[在 APPLICATION INSIGHTS SDK NuGet 套件上新增](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)相依性。 這種方法也表示您必須自行管理最新版本套件的更新。

    * 如果您需要進行自訂 API 呼叫來追蹤預設不是以代理程式為基礎所捕捉的事件/相依性，則必須使用此方法。 若要深入瞭解，請參閱[自訂事件和計量的 API 一文](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)。

> [!NOTE]
> 如果偵測到以代理程式為基礎的監視和以手動 SDK 為基礎的檢測，則只會接受手動檢測設定。 這是為了防止傳送重複的資料。 若要深入瞭解，請參閱下面的[疑難排解一節](#troubleshooting)。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>使用 PowerShell 管理 Azure 虛擬機器上的 .NET 應用程式 Application Insights 代理程式

> [!NOTE]
> 在安裝 Application Insights 代理程式之前，您需要連接字串。 [建立新的 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)，或從現有的 application Insights 資源複製連接字串。

> [!NOTE]
> 不熟悉 powershell 嗎？ 請參閱開始使用[指南](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0)。

安裝或更新 Application Insights 代理程式做為 Azure 虛擬機器的擴充功能
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> 您可以使用 Powershell 迴圈，將 Application Insights 代理程式安裝或更新為跨多個虛擬機器的延伸模組。

從 Azure 虛擬機器卸載 Application Insights 代理程式擴充功能
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Azure 虛擬機器的查詢 Application Insights 代理程式延伸模組狀態
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

取得 Azure 虛擬機器的已安裝擴充功能清單
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
您也可以在入口網站的 [ [Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/extensions/overview)] 分頁中，看到已安裝的延伸模組。

> [!NOTE]
> 在與您用來部署 Application Insights 代理程式擴充功能的連接字串相關聯的 Application Insights 資源中，按一下即時計量資料流，以確認安裝。 如果您從多個虛擬機器傳送資料，請選取 [伺服器名稱] 下的 [目標 Azure 虛擬機器]。 最多可能需要一分鐘的時間，資料才會開始流動。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>使用 powershell 管理 Azure 虛擬機器擴展集上的 .NET 應用程式 Application Insights 代理程式

安裝或更新 Application Insights 代理程式做為 Azure 虛擬機器擴展集的擴充功能
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

從 Azure 虛擬機器擴展集卸載應用程式監視擴充功能
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 虛擬機器擴展集的查詢應用程式監視延伸模組狀態
```powershell
# Not supported by extensions framework
```

取得 Azure 虛擬機器擴展集已安裝的擴充功能清單
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>疑難排解

針對在 Azure 虛擬機器和虛擬機器擴展集上執行的 .NET 應用程式，尋找 Application Insights Monitoring Agent 擴充功能的疑難排解秘訣。

> [!NOTE]
> 只有 Azure 虛擬機器和 Azure 虛擬機器擴展集上的 .NET Core、JAVA 和 Node.js 應用程式可透過手動以 SDK 為基礎的檢測來支援，因此，下列步驟不適用於這些案例。

延伸模組執行輸出會記錄至下列目錄中的檔案：
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>後續步驟
* 瞭解如何將[應用程式部署至 Azure 虛擬機器擴展集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)。
* [設定可用性 web 測試](monitor-web-app-availability.md)，以在您的端點關閉時收到警示。
