---
title: 監視 Azure Vm 上的效能-Azure 應用程式見解
description: Azure VM 和 Azure 虛擬機器擴展集的應用程式效能監視。 圖表載入和回應時間、相依性資訊，以及設定效能警示。
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 0ea005427348e5265867a9e7ee805b0e6aa202f2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933903"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>在 Azure 虛擬機器和 Azure 虛擬機器擴展集上部署 Azure 監視器 Application Insights 代理程式

現在，在 [azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/) 和 [azure 虛擬機器擴展集](../../virtual-machine-scale-sets/index.yml) 上執行的 .net web 應用程式上啟用監視，現在比以往更容易。 取得使用 Application Insights 的所有優點，而不需要修改您的程式碼。

本文將逐步引導您使用 Application Insights 代理程式來啟用 Application Insights 監視，並提供將大規模部署程式自動化的初步指引。

> [!IMPORTANT]
> 針對在 **Azure vm 和 VMSS** 上執行的 ASP.NET 應用程式，Azure 應用程式 Insights 代理程式目前處於公開預覽狀態。 若要監視在 **內部部署** 環境中執行的 ASP.Net 應用程式，請使用適用于內部 [部署伺服器的 Azure 應用程式 Insights 代理](./status-monitor-v2-overview.md)程式，其已正式運作並受到完整支援。
> Azure Vm 和 VMSS 的預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 某些功能可能不受支援，有些功能可能會有有限的功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enable-application-insights"></a>啟用 Application Insights

有兩種方式可以針對 Azure 虛擬機器和 Azure 虛擬機器擴展集託管應用程式啟用應用程式監視：

* **無程式碼** Via Application Insights 代理程式
    * 此方法最容易啟用，而且不需要進行任何 advanced 設定。 它通常稱為「執行時間」監視。

    * 針對 Azure 虛擬機器和 Azure 虛擬機器擴展集，我們建議您至少啟用此層級的監視。 之後，您可以根據您的特定案例，評估是否需要手動檢測。

    * Application Insights 代理程式會自動收集與 .NET SDK 完全相同的相依性信號。 若要深入瞭解，請參閱相依性 [自動收集](./auto-collect-dependencies.md#net) 。
        > [!NOTE]
        > 目前只支援 .Net IIS 託管的應用程式。 使用 SDK 來檢測裝載在 Azure 虛擬機器和虛擬機器擴展集上的 ASP.NET Core、JAVA 和 Node.js 應用程式。

* 以 **程式碼為基礎的** via SDK

    * 這種方法更容易自訂，但需要 [在 APPLICATION INSIGHTS SDK NuGet 套件上新增](./asp-net.md)相依性。 這種方法也表示您必須自行管理套件最新版本的更新。

    * 如果您需要進行自訂 API 呼叫來追蹤未依預設使用代理程式監視所產生的事件/相依性，則必須使用此方法。 若要深入瞭解，請參閱 [自訂事件和計量文章的 API](./api-custom-events-metrics.md) 。

> [!NOTE]
> 如果偵測到以代理程式為基礎的監視和手動以 SDK 為基礎的檢測，則只會接受手動檢測設定。 這是為了防止傳送重複的資料。 若要深入瞭解，請參閱下面的 [疑難排解一節](#troubleshooting) 。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>使用 PowerShell 管理 Azure 虛擬機器上適用于 .NET 應用程式的 Application Insights 代理程式

> [!NOTE]
> 在安裝 Application Insights 代理程式之前，您需要連接字串。 [建立新的 Application Insights 資源](./create-new-resource.md) ，或從現有的 application Insights 資源複製連接字串。

> [!NOTE]
> 剛開始進行 powershell 嗎？ 請參閱 [開始指南](/powershell/azure/get-started-azureps)。

將 Application Insights 代理程式安裝或更新為 Azure 虛擬機器的擴充功能
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
> 您可以使用 Powershell 迴圈，在多部虛擬機器之間安裝或更新 Application Insights 代理程式作為擴充功能。

從 Azure 虛擬機器卸載 Application Insights 代理程式延伸模組
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Application Insights Azure 虛擬機器的代理程式擴充狀態查詢
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

取得適用于 Azure 虛擬機器的已安裝擴充功能清單
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
您也可以在入口網站的 [ [Azure 虛擬機器](../../virtual-machines/extensions/overview.md) ] 分頁中，查看已安裝的擴充功能。

> [!NOTE]
> 按一下與您用來部署 Application Insights 代理程式擴充功能的連接字串相關聯的 Application Insights 資源內即時計量資料流，以確認安裝。 如果您要從多個虛擬機器傳送資料，請選取 [伺服器名稱] 下的 [目標 Azure 虛擬機器]。 資料最多可能需要一分鐘的時間才會開始流動。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>使用 powershell 管理 Azure 虛擬機器擴展集上的 .NET 應用程式 Application Insights 代理程式

將 Application Insights 代理程式安裝或更新為 Azure 虛擬機器擴展集的擴充功能
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

從 Azure 虛擬機器擴展集卸載應用程式監視延伸模組
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

查詢 Azure 虛擬機器擴展集的應用程式監視擴充功能狀態
```powershell
# Not supported by extensions framework
```

取得適用于 Azure 虛擬機器擴展集的已安裝擴充功能清單
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>疑難排解

針對在 Azure 虛擬機器和虛擬機器擴展集上執行的 .NET 應用程式，尋找 Application Insights 監視代理程式擴充功能的疑難排解秘訣。

> [!NOTE]
> 只有在 Azure 虛擬機器和 Azure 虛擬機器擴展集上，才會透過手動以 SDK 為基礎的檢測來支援 .NET Core、JAVA 和 Node.js 的應用程式，因此下列步驟不適用於這些案例。

延伸模組執行輸出會記錄至下列目錄中的檔案：
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>後續步驟
* 瞭解如何將 [應用程式部署至 Azure 虛擬機器擴展集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)。
* [設定可用性 web 測試](monitor-web-app-availability.md) ，以在您的端點關閉時收到警示。
