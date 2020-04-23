---
title: 教學課程 - 在 Azure 中平衡 Windows 虛擬機器的負載
description: 在本教學課程中，您會了解如何使用 Azure PowerShell 來建立負載平衡器，以在三個 Windows 虛擬機器之間獲得高可用性和安全的應用程式
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 781ee9136c13f75ab53c662225cc12b28b1fb02e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455694"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>教學課程：使用 Azure PowerShell 平衡 Azure 中 Windows 虛擬機器的負載以建立高可用性應用程式
負載平衡會將傳入要求分散到多部虛擬機器，藉此提供高可用性。 在本教學課程中，您會了解 Azure Load Balancer 的不同元件，以分散流量並提供高可用性。 您會了解如何：

> [!div class="checklist"]
> * 建立 Azure Load Balancer
> * 建立負載平衡器健全狀況探查
> * 建立負載平衡器流量規則
> * 使用自訂指令碼擴充功能建立基本 IIS 網站
> * 建立虛擬機器並連結至負載平衡器
> * 檢視作用中的負載平衡器
> * 新增和移除虛擬機器的負載平衡器

## <a name="azure-load-balancer-overview"></a>Azure Load Balancer 概觀
Azure Load Balancer 是 Layer-4 (TCP、UDP) 負載平衡器，可將連入流量分散於狀況良好的 VM 來提供高可用性。 負載平衡器健康狀態探查會監視每部 VM 上指定的連接埠，且只會將流量分散至作業 VM。

您可定義含有一或多個公用 IP 位址的前端 IP 組態。 此前端 IP 組態允許透過網際網路存取您的負載平衡器和應用程式。 

虛擬機器會使用他其虛擬網路介面卡 (NIC) 連線至負載平衡器。 若要將流量分散至 VM，後端位址集區包含已連線至負載平衡器之虛擬 (NIC) 的 IP 位址。

為了控制流量，您可以定義特定連接埠的負載平衡器規則以及對應至您的 VM 的通訊協定。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="create-azure-load-balancer"></a>建立 Azure Load Balancer
本節將詳細說明如何建立及設定負載平衡器的每個元件。 請先使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組，才可建立負載平衡器。 下列範例會在 EastUS  位置建立名為 myResourceGroupLoadBalancer  的資源群組：

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址
若要存取網際網路上您的應用程式，您需要負載平衡器的公用 IP 位址。 使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) 建立公用 IP 位址。 下列範例會在 myResourceGroupLoadBalancer  資源群組中建立名為 myPublicIP  的公用 IP 位址：

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>建立負載平衡器
使用 [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP 集區。 下列範例會建立名為 *myFrontEndPool* 的前端 IP 集區，並連結 *myPublicIP* 位址： 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

使用 [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 建立後端位址集區。 在其餘步驟中，VM 會連結至此後端集區。 下列範例會建立名為 myBackEndPool  的後端位址集區：

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

現在，使用 [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) 建立負載平衡器。 下列範例會使用在先前的步驟中建立的後端 IP 集區，建立名為 *myLoadBalancer* 的負載平衡器：

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>建立健康狀態探查
若要讓負載平衡器監視您應用程式的狀態，請使用健康狀態探查。 健康狀態探查會根據 VM 對健康狀態檢查的回應，以動態方式從負載平衡器輪替中新增或移除 VM。 根據預設，在 15 秒的間隔內連續發生兩次失敗後，VM 就會從負載平衡器分配中移除。 您可根據通訊協定或您應用程式的特定健康狀態檢查頁面，建立健康狀態探查。 

下列範例會建立 TCP 探查。 您也可以建立自訂 HTTP 探查，以進行更精細的健康狀態檢查。 使用自訂 HTTP 探查時，您必須建立健康狀態檢查頁面，例如 healthcheck.aspx  。 此探查必須對負載平衡器傳回 **HTTP 200 OK** 回應，以將主機保留在輪替中。

若要建立 TCP 健康狀態探查，請使用 [Add-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/add-azloadbalancerprobeconfig)。 下列範例會建立名為 *myHealthProbe* 的健康狀態探查，在 *TCP* 連接埠 *80* 上監視每個 VM：

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

若要套用健康情況探查，請使用 [Set-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/set-azloadbalancer) 更新負載平衡器：

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則
負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 若要確定只有狀況良好的 VM 可接收流量，您也可定義要使用的健康狀態探查。

使用 [Add-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則。 下列範例會建立名為 *myLoadBalancerRule* 的負載平衡器規則，並平衡 *TCP* 連接埠 *80* 上的流量：

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

使用 [Set-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/set-azloadbalancer) 更新負載平衡器：

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>設定虛擬網路
請先建立支援的虛擬網路資源，才可部署一些 VM 及測試您的平衡器。 如需虛擬網路的詳細資訊，請參閱[管理 Azure 虛擬網路](tutorial-virtual-network.md)教學課程。

### <a name="create-network-resources"></a>建立網路資源
使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。 下列範例會建立名為 myVnet  的虛擬網路和 mySubnet  ：

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) 建立虛擬 NIC。 下列範例會建立三個虛擬 NIC。 (您在下列步驟中針對應用程式建立的每部 VM 都有一個虛擬 NIC)。 您可以隨時建立其他虛擬 NIC 和 VM，並將它們新增至負載平衡器︰

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>建立虛擬機器
若要改善您應用程式的高可用性，請將 VM 放在可用性設定組中。

使用 [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) 建立可用性設定組。 下列範例會建立名為 myAvailabilitySet  的可用性設定組：

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

現在您可以使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 建立 VM。 下列範例會建立三個 VM，並建立必要的虛擬網路元件 (如果尚未存在)︰

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

`-AsJob` 參數會以背景工作建立 VM，因此會傳回 PowerShell 提示。 您可以使用 `Job` Cmdlet 檢視背景作業的詳細資料。 建立及設定這三部 VM 需要幾分鐘的時間。


### <a name="install-iis-with-custom-script-extension"></a>安裝 IIS 與自訂指令碼擴充功能
在[如何自訂 Windows 虛擬機器](tutorial-automate-vm-deployment.md)的先前教學課程中，您已了解如何使用適用於 Windows 的自訂指令碼擴充功能自動進行 VM 自訂。 您可以使用相同的方式在您的 VM 上安裝及設定 IIS。

使用 [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) 來安裝自訂指令碼擴充功能。 擴充功能會執行 `powershell Add-WindowsFeature Web-Server` 以安裝 IIS Web 伺服器，然後更新 Default.htm  頁面以顯示 VM 的主機名稱：

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>測試負載平衡器
使用 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 取得負載平衡器的公用 IP 位址。 下列範例會取得稍早建立的 myPublicIP  IP 位址︰

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

接著，您可以在 Web 瀏覽器中輸入公用 IP 位址。 網站隨即顯示，包括負載平衡器分散流量之 VM 的主機名稱，如下列範例所示：

![執行中的 IIS 網站](./media/tutorial-load-balancer/running-iis-website.png)

若要查看負載平衡器如何將流量分散於執行您應用程式的這三部 VM，您可以強制重新整理您的 Web 瀏覽器。


## <a name="add-and-remove-vms"></a>新增和移除 VM
您可能需要在執行您應用程式的 VM 上執行維護，例如安裝 OS 更新。 若要處理您應用程式增加的流量，您可能需要新增額外的 VM。 本節說明如何在負載平衡器中移除或新增 VM。

### <a name="remove-a-vm-from-the-load-balancer"></a>從負載平衡器移除 VM
使用 [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) 取得網路介面卡，然後將虛擬 NIC 的 LoadBalancerBackendAddressPools  屬性設定為 $null  。 最後，更新虛擬 NIC：

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

若要查看負載平衡器如何將流量分散到其餘兩部執行您應用程式的 VM，您可以強制重新整理您的 Web 瀏覽器。 您現在可以在 VM 上執行維護，例如安裝 OS 更新或執行 VM 重新開機。

### <a name="add-a-vm-to-the-load-balancer"></a>將 VM 新增至負載平衡器
在執行 VM 維護之後，或者如果需要擴充容量，請經由 [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) 將虛擬 NIC 的 LoadBalancerBackendAddressPools  屬性設定為 BackendAddressPool  ：

取得負載平衡器：

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立負載平衡器，並將 VM 連結到它。 您已了解如何︰

> [!div class="checklist"]
> * 建立 Azure Load Balancer
> * 建立負載平衡器健全狀況探查
> * 建立負載平衡器流量規則
> * 使用自訂指令碼擴充功能建立基本 IIS 網站
> * 建立虛擬機器並連結至負載平衡器
> * 檢視作用中的負載平衡器
> * 新增和移除虛擬機器的負載平衡器

前進至下一個教學課程，以了解如何管理 VM 網路功能。

> [!div class="nextstepaction"]
> [管理 VM 和虛擬網路](./tutorial-virtual-network.md)
