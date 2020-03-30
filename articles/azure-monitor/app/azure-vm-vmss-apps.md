---
title: 監視 Azure VM 上的性能 - Azure 應用程式見解
description: Azure VM 和 Azure 虛擬機器縮放集的應用程式效能監控。 圖表載入和回應時間、依賴項資訊以及設置性能警報。
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661323"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>在 Azure 虛擬機器和 Azure 虛擬機器縮放集上部署 Azure 監視器應用程式見解代理

現在，在[Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)和[Azure 虛擬機器縮放集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)上運行的基於 .NET 的 Web 應用程式啟用監視現在比以往任何時候都更容易。 無需修改代碼即可獲得使用應用程式見解的所有好處。

本文將引導您使用應用程式見解代理啟用應用程式見解監視，並提供大規模部署過程自動化的初步指導。

> [!IMPORTANT]
> .NET 的 Azure 應用程式見解代理當前處於公共預覽版中。
> 此預覽版本在沒有服務等級協定的情況下提供，我們不建議用於生產工作負載。 某些功能可能不受支援，有些功能可能受限。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enable-application-insights"></a>啟用 Application Insights

有兩種方法可以為 Azure 虛擬機器和 Azure 虛擬機器縮放集託管應用程式啟用應用程式監視：

* 通過應用程式洞察代理**無代碼**
    * 此方法是最容易啟用的，不需要高級配置。 它通常被稱為"運行時"監視。

    * 對於 Azure 虛擬機器和 Azure 虛擬機器縮放集，我們建議至少啟用此級別的監視。 之後，根據您的特定方案，您可以評估是否需要手動檢測。

    * 應用程式見解代理自動收集與 .NET SDK 相同的依賴項信號。 請參閱[依賴項自動收集](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net)以瞭解更多資訊。
        > [!NOTE]
        > 目前僅支援 .Net IIS 託管的應用程式。 使用 SDK 檢測 ASP.NET在 Azure 虛擬機器和虛擬機器縮放集上託管的酷睿、JAVA 和 Node.js 應用程式。

* 通過 SDK**基於代碼**

    * 此方法更具可自訂性，但它需要[添加對應用程式見解 SDK NuGet 包的依賴項](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)。 此方法還意味著您必須自己管理最新版本的包的更新。

    * 如果需要進行自訂 API 呼叫來跟蹤預設情況下未通過基於代理的監視捕獲的事件/依賴項，則需要使用此方法。 請查看[API 中的自訂事件和指標文章](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)以瞭解更多資訊。

> [!NOTE]
> 如果檢測到兩個基於代理的監視和基於手動 SDK 的檢測，則僅遵守手動檢測設置。 這是為了防止發送重復資料。 要瞭解有關此詳細資訊，請查看下面的[故障排除部分](#troubleshooting)。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>使用 PowerShell 在 Azure 虛擬機器上管理 .NET 應用程式的應用程式見解代理

> [!NOTE]
> 在安裝應用程式見解代理之前，您需要一個連接字串。 [創建新的應用程式見解資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)或從現有應用程式見解資源複製連接字串。

> [!NOTE]
> 權力殼的新鮮？ 查看入門[指南](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0)。

安裝或更新應用程式見解代理作為 Azure 虛擬機器的擴展
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
> 您可以使用 Powershell 迴圈安裝或更新應用程式見解代理，作為跨多個虛擬機器規模的擴展。

從 Azure 虛擬機器卸載應用程式見解代理擴展
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Azure 虛擬機器的查詢應用程式見解代理擴展狀態
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

獲取 Azure 虛擬機器的已安裝擴展清單
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
您還可以在門戶中的[Azure 虛擬機器邊欄選項卡](https://docs.microsoft.com/azure/virtual-machines/extensions/overview)中查看已安裝的擴展。

> [!NOTE]
> 通過按一下與用於部署應用程式見解代理擴展的連接字串關聯的應用程式見解資源中的即時指標流來驗證安裝。 如果要從多個虛擬機器發送資料，請在"伺服器名稱"下選擇目標 Azure 虛擬機器。 資料可能需要一分鐘才能開始流動。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>使用電源外殼在 Azure 虛擬機器縮放集中管理 .NET 應用程式的應用程式見解代理

安裝或更新應用程式見解代理作為 Azure 虛擬機器縮放集的擴展
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

從 Azure 虛擬機器縮放集卸載應用程式監視擴展
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 虛擬機器縮放集的查詢應用程式監視擴展狀態
```powershell
# Not supported by extensions framework
```

獲取 Azure 虛擬機器縮放集的已安裝擴展清單
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>疑難排解

查找在 Azure 虛擬機器和虛擬機器規模集中運行的 .NET 應用程式的應用程式見解監視代理擴展的故障排除提示。

> [!NOTE]
> .NET Core、JAVA 和 Node.js 應用程式僅支援 Azure 虛擬機器和 Azure 虛擬機器縮放集，通過基於 SDK 的手動檢測，因此以下步驟不適用於這些方案。

延伸模組執行輸出會記錄至下列目錄中的檔案：
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>後續步驟
* 瞭解如何[將應用程式部署到 Azure 虛擬機器縮放集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)。
* [設置可用性 Web 測試](monitor-web-app-availability.md)，以便終結點關閉時收到警報。
