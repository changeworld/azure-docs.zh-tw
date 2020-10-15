---
title: 教學課程：建立 Windows 虛擬機器擴展集
description: 了解如何使用 Azure PowerShell 在 Windows VM 上使用虛擬機器擴展集，建立及部署高可用性應用程式
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 11/30/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: b3853ddc71d1a9be26b2492764a9b341446e0eeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078736"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>教學課程：使用 Azure PowerShell 在 Windows 上建立虛擬機器擴展集及部署高可用性應用程式
虛擬機器擴展集可讓您部署和管理一組相同、自動調整的虛擬機器。 您可以手動調整擴展集中的 VM 數目。 您也可以定義規則以根據如 CPU、記憶體需求或網路流量的資源使用量來自動調整。 在本教學課程中，您將會在 Azure 部署虛擬機器擴展集，並了解如何：

> [!div class="checklist"]
> * 使用自訂指令碼擴充功能定義可擴展的 IIS 網站
> * 建立擴展集的負載平衡器規則
> * 建立虛擬機器擴展集
> * 增加或減少擴展集內的執行個體數目
> * 建立自動調整規則

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="scale-set-overview"></a>擴展集概觀
虛擬機器擴展集可讓您部署和管理一組相同、自動調整的虛擬機器。 擴展集中的 VM 會分散於一或多個放置群組  中的邏輯容錯網域和更新網域。 放置群組是類似已設定 VM 的群組，類似於[可用性設定組](tutorial-availability-sets.md)。

VM 會視需要建立於擴展集中。 您將定義自動調整規則，以控制如何及何時新增或移除擴展集中的 VM。 您可以根據計量 (例如 CPU 負載、記憶體使用量或網路流量) 觸發這些規則。

當您使用 Azure 平台映像時，擴展集可支援多達 1,000 部 VM。 對於包含重要安裝作業或 VM 自訂要求的工作負載，您可能想要[建立自訂的 VM 映像](tutorial-custom-images.md)。 使用自訂映像時，您可以在擴展集中建立多達 600 部 VM。


## <a name="create-a-scale-set"></a>建立擴展集
使用 [New-AzVmss](/powershell/module/az.compute/new-azvmss) 建立虛擬機器擴展集。 下列範例會建立使用 Windows Server 2016 資料中心  平台映像，名為 myScaleSet  的擴展集。 系統會自動建立虛擬網路、公用 IP 位址和負載平衡器的 Azure 網路資源。 出現提示時，您可以為擴展集中的 VM 執行個體設定自己的系統管理認證：

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。


## <a name="deploy-sample-application"></a>部署範例應用程式
若要測試您的擴展集，請安裝基本的 Web 應用程式。 您可以使用 Azure 自訂指令碼延伸模組來下載及執行會在 VM 執行個體上安裝 IIS 的指令碼。 此擴充功能適用於部署後組態、軟體安裝或其他任何組態/管理工作。 如需詳細資訊，請參閱[自訂指令碼延伸模組概觀](../extensions/custom-script-windows.md)。

使用自訂指令碼延伸模組安裝基本的 IIS Web 伺服器。 套用安裝 IIS 的自訂指令碼延伸模組，如下所示：

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>允許流量流向應用程式

若要允許存取基本 Web 應用程式，請使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 和 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 建立網路安全性群組。 如需詳細資訊，請參閱 [Azure 虛擬機器擴展集的網路功能](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md)。

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>測試您的擴展集
若要查看作用中的擴展集，可使用 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) 取得負載平衡器的公用 IP 位址。 下列範例會顯示建立作為擴展集一部分的 myPublicIP  IP 位址︰

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myPublicIPAddress" | select IpAddress
```

在 Web 瀏覽器中輸入公用 IP 位址。 Web 應用程式隨即顯示，包括負載平衡器分散流量之 VM 的主機名稱：

![執行中的 IIS 網站](./media/tutorial-create-vmss/running-iis-site.png)

若要查看作用中的擴展集，請強制重新整理您的 Web 瀏覽器，以查看負載平衡器如何將流量分散於執行應用程式的所有 VM。


## <a name="management-tasks"></a>管理工作
在擴展集生命週期中，您可能需要執行一或多個管理工作。 此外，您可以建立指令碼來自動化各種生命週期工作。 Azure PowerShell 提供快速的方式來執行這些工作。 以下是一些常見工作。

### <a name="view-vms-in-a-scale-set"></a>檢視擴展集中的 VM
若要檢視擴展集中的 VM 執行個體清單，請使用 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)，如下所示：

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"
```

下列範例輸出顯示擴展集中的兩個 VM 執行個體：

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

若要檢視特定 VM 執行個體的其他資訊，請將 `-InstanceId` 參數新增至 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)。 下列範例顯示 VM 執行個體 *1* 的相關資訊：

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" `
  -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>增加或減少 VM 執行個體
若要查看擴展集中目前擁有的執行個體數目，請使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) 並查詢 sku.capacity  ：

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" | `
  Select -ExpandProperty Sku
```

然後，您可以使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)，手動增加或減少擴展集中的虛擬機器數目。 下列範例會將擴展集中的 VM 數目設定為 *3*：

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

如果更新擴展集中指定的執行個體數目需費時幾分鐘。


### <a name="configure-autoscale-rules"></a>設定自動調整規則
除了手動調整擴展集中的執行個體數目，您可以定義自動調整規則。 這些規則會監視擴展集中的執行個體，並根據您定義的計量和臨界值進行回應。 下列範例會示範當 CPU 平均負載大於 60% 並持續 5 分鐘以上時，如何增加一個執行個體來相應放大執行個體數目。 如果之後 CPU 平均負載降到低於 30% 且持續 5 分鐘以上，則減少一個執行個體來相應縮小執行個體數目：

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

如需使用自動調整的詳細設計資訊，請參閱[自動調整最佳做法](/azure/architecture/best-practices/auto-scaling)。


## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立虛擬機器擴展集。 您已了解如何︰

> [!div class="checklist"]
> * 使用自訂指令碼擴充功能定義可擴展的 IIS 網站
> * 建立擴展集的負載平衡器規則
> * 建立虛擬機器擴展集
> * 增加或減少擴展集內的執行個體數目
> * 建立自動調整規則

請前進到下一個教學課程，以深入了解虛擬機器的負載平衡概念。

> [!div class="nextstepaction"]
> [平衡虛擬機器的負載](tutorial-load-balancer.md)
