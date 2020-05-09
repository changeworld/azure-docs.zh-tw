---
title: 搭配 Azure 虛擬機器擴展集使用應用程式健康狀態延伸模組
description: 了解如何使用應用程式健康狀態延伸模組，以監視虛擬機器擴展集上所部署應用程式的健康狀態。
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: mimckitt
ms.openlocfilehash: 30f68d22a228e6de596e6999490ea7789ab21547
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864363"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>搭配虛擬機器擴展集使用應用程式健康狀態延伸模組
監視應用程式健康狀態是用於管理及升級部署的重要訊號。 Azure 虛擬機器擴展集支援包括[自動 OS 映像升級](virtual-machine-scale-sets-automatic-upgrade.md)的[輪流升級](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)，這些升級仰賴個別執行個體的健康狀態監視來升級您的部署。 您也可以使用健康情況延伸模組來監視擴展集中每個實例的應用程式健全狀況，並使用[自動實例修復](virtual-machine-scale-sets-automatic-instance-repairs.md)來執行實例修復。

本文描述如何使用應用程式健康狀態延伸模組，以監視虛擬機器擴展集上所部署應用程式的健康狀態。

## <a name="prerequisites"></a>必要條件
本文假設您已熟悉以下各項：
-   Azure 虛擬機器[擴充](../virtual-machines/extensions/overview.md)功能
-   [修改](virtual-machine-scale-sets-upgrade-scale-set.md)虛擬機器擴展集

## <a name="when-to-use-the-application-health-extension"></a>使用應用程式健康狀態延伸模組的時機
應用程式健康狀態延伸模組會部署在虛擬機器擴展集執行個體內部，並從擴展集執行個體內部報告 VM 健康狀態。 您可以將延伸模組設定為在應用程式端點上探查，並更新該執行個體上的應用程式狀態。 Azure 會檢查此執行個體狀態，以判斷執行個體是否適合進行升級作業。

因為此擴充功能是從 VM 內報告健康情況，所以在無法使用應用程式健康情況探查 (其利用自訂的 Azure Load Balancer [探查](../load-balancer/load-balancer-custom-probe-overview.md)) 等外部探查的情況下，可以使用此擴充功能。

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 會顯示應用程式健康狀態延伸模組的結構描述。 延伸模組需要至少有一個相關聯埠或要求路徑的 "tcp"、"HTTP" 或 "HTTPs" 要求。

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | 字串 |
| type | `ApplicationHealthLinux` (Linux)，`ApplicationHealthWindows` (Windows) | 字串 |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>設定

| 名稱 | 值 / 範例 | 資料類型
| ---- | ---- | ----
| protocol | `http`、`https` 或 `tcp` | 字串 |
| 連接埠 | 當通訊協定為`http`或`https`時為選擇性，當通訊協定為時為必要`tcp` | int |
| requestPath | 當通訊協定是`http`或`https`時，不允許使用`tcp` | 字串 |

## <a name="deploy-the-application-health-extension"></a>部署應用程式健康狀態延伸模組
有多種方法可以將應用程式健康狀態延伸模組部署至您的擴展集，如下面的範例所詳述。

### <a name="rest-api"></a>REST API

下列範例會將應用程式健康情況擴充功能 (名稱為 myHealthExtension) 新增至 extensionProfile (位於 Windows 型擴展集的擴展集模型中)。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
請使用 `PATCH` 來編輯已部署的延伸模組。

### <a name="azure-powershell"></a>Azure PowerShell

使用 [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) Cmdlet，將應用程式健康情況擴充功能新增至擴展集模型定義。

下列範例會將應用程式健康情況擴充功能新增至 `extensionProfile` (位於 Windows 型擴展集的擴展集模型中)。 此範例使用新的 Az PowerShell 模組。

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

使用 [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set)，可將應用程式健康狀態延伸模組新增至擴展集模型定義。

下列範例會將應用程式健康情況擴充功能新增至以 Linux 為基礎的擴展集的擴展集模型。

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
擴充功能 json 檔案內容。

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>疑難排解
延伸模組執行輸出會記錄至下列目錄中的檔案：

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

記錄也會定期擷取應用程式健康狀態狀態。

## <a name="next-steps"></a>後續步驟
了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
