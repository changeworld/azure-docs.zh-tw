---
title: 部署 & 使用 PowerShell 在混合式網路中設定 Azure 防火牆
description: 在本文中，您將瞭解如何使用 Azure PowerShell 來部署和設定 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/08/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 802df45e7434fd0cb425137964880a281f885ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611164"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>使用 Azure PowerShell 在混合式網路中部署及設定 Azure 防火牆

當您將內部部署網路連線到 Azure 虛擬網路以建立混合式網路時，控制 Azure 網路資源存取的能力是整體安全性計劃的重要部分。

您可以使用定義允許和拒絕網路流量的規則，在混合式網路中使用 Azure 防火牆來控制網路存取。

在本文中，您會建立三個虛擬網路：

- **VNet-Hub** - 防火牆位於此虛擬網路中。
- **VNet-Spoke** - 輪輻虛擬網路代表位於 Azure 的工作負載。
- **VNet-Onprem** - 內部部署虛擬網路代表內部部署網路。 在實際部署中，它可經由 VPN 或 ExpressRoute 連線來連線。 為了簡單起見，本文使用 VPN 閘道連線，而 Azure 位置的虛擬網路則用來代表內部部署網路。

![混合式網路中的防火牆](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

在本文中，您將學會如何：

> [!div class="checklist"]
> * 宣告變數
> * 建立防火牆中樞虛擬網路
> * 建立輪輻虛擬網路
> * 建立內部部署虛擬網路
> * 設定及部署防火牆
> * 建立及連線 VPN 閘道
> * 對等互連中樞與輪輻虛擬網路
> * 建立路由
> * 建立虛擬機器
> * 測試防火牆

如果您想要改為使用 Azure 入口網站來完成本教學課程，請參閱[教學課程：使用 Azure 入口網站在混合式網路中部署和設定 Azure 防火牆](tutorial-hybrid-portal.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

本文會要求您在本機執行 PowerShell。 您必須已安裝 Azure PowerShell 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-Az-ps)。 驗證 PowerShell 版本之後，請執行 `Login-AzAccount` 以建立與 Azure 的連線。

要讓此案例正常運作有三項重要需求：

- 輪輻子網路上使用者定義的路由 (UDR) 會指向 Azure 防火牆 IP 位址，作為預設閘道。 必須**停用**此路由表上的虛擬網路閘道路由傳播。
- 中樞閘道子網路上的 UDR 必須指向防火牆 IP 位址，作為輪輻網路的下一個躍點。

   Azure 防火牆子網路不需要任何 UDR，因為可從 BGP 得知路由。
- 請務必在將 VNet-Hub 對等互連至 VNet-Spoke 時設定 **AllowGatewayTransit**，以及在將 VNet-Spoke 對等互連至 VNet-Hub 時設定 **UseRemoteGateways**。

請參閱本文中的[建立路由](#create-the-routes)一節，以瞭解如何建立這些路由。

>[!NOTE]
>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 學習到透過 BGP 連至您內部部署網路的預設路由，您必須將其覆寫為 0.0.0.0/0 UDR，且 **NextHopType** 值必須設為 [網際網路]****，以保有直接網際網路連線。
>
>您可將 Azure 防火牆設定為支援強制通道。 如需詳細資訊，請參閱 [Azure 防火牆強制通道](forced-tunneling.md)。

>[!NOTE]
>即使 UDR 指向「Azure 防火牆」作為預設閘道，系統仍會直接路由直接對等互連之 VNet 間的流量。 在此案例中若要將子網路對子網路流量傳送到防火牆，UDR 必須在這兩個子網路上同時明確包含目標子網路網路首碼。

若要檢閱相關的 Azure PowerShell 參考文件，請參閱 [Azure PowerShell 參考](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="declare-the-variables"></a>宣告變數

下列範例會使用本文的值宣告變數。 在某些情況下，您可能需要以自己的值取代一些值，才能在您的訂用帳戶中運作。 視需要修改變數，然後將其複製並貼到您的 PowerShell 主控台中。

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>建立防火牆中樞虛擬網路

首先，建立資源群組以包含本文的資源：

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

定義要包含在虛擬網路中的子網路：

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

現在，建立防火牆中樞虛擬網路：

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

要求一個公用 IP 位址，以配置給您將建立給虛擬網路使用的 VPN 閘道。 請注意，AllocationMethod** 為 [動態]****。 您無法指定想要使用的 IP 位址。 該 IP 位址會以動態方式配置給您的 VPN 閘道。

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>建立輪輻虛擬網路

定義要包含在輪輻虛擬網路中的子網路：

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

建立輪輻虛擬網路：

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>建立內部部署虛擬網路

定義要包含在虛擬網路中的子網路：

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

現在，建立內部部署虛擬網路：

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

要求一個公用 IP 位址，以配置給您將建立給虛擬網路使用的閘道。 請注意，AllocationMethod** 為 [動態]****。 您無法指定想要使用的 IP 位址。 該 IP 位址會以動態方式配置給您的閘道。

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>設定及部署防火牆

現在將防火牆部署到中樞虛擬網路中。

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>設定網路規則

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>建立及連線 VPN 閘道

中樞和內部部署虛擬網路都是透過 VPN 閘道連線。

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>建立中樞虛擬網路的 VPN 閘道

建立 VPN 閘道組態。 VPN 閘道器組態定義要使用的子網路和公用 IP 位址。

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

現在建立中樞虛擬網路的 VPN 閘道。 網路對網路組態需要 RouteBased VpnType。 建立 VPN 閘道通常可能需要 45 分鐘或更久，視選取的 VPN 閘道 SKU 而定。

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>建立虛擬網路的 VPN 內部部署閘道

建立 VPN 閘道組態。 VPN 閘道器組態定義要使用的子網路和公用 IP 位址。

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

現在建立虛擬網路的 VPN 內部部署閘道。 網路對網路組態需要 RouteBased VpnType。 建立 VPN 閘道通常可能需要 45 分鐘或更久，視選取的 VPN 閘道 SKU 而定。

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>建立 VPN 連線

您現在可以建立中樞與內部部署閘道之間的 VPN 連線

#### <a name="get-the-vpn-gateways"></a>取得 VPN 閘道

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>建立連線

在此步驟中，您會建立從中樞虛擬網路到內部部署虛擬網路的連線。 您會看到範例使用共用金鑰。 您可以使用自己的值，作為共用金鑰。 但請務必確認該共用金鑰必須適用於這兩個連線。 建立連線可能需要一段時間才能完成。

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
建立內部部署對中樞虛擬網路連線。 此步驟類似前一個步驟，只不過您是建立 VNet-Onprem 到 VNet-hub 的連線。 請確認共用的金鑰相符。 稍候幾分鐘就會建立連線。

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>驗證連線

您可以使用 Get-AzVirtualNetworkGatewayConnection** Cmdlet，並在搭配或不搭配 -Debug** 的情況下驗證連線是否成功。 請使用下列 Cmdlet 範例，並將值設定為與您狀況相符的值。 出現提示時，請選取 **A** 以執行 [全部]****。 在此範例中，-Name** 是指您想要測試的連線名稱。

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

完成 Cmdlet 之後，請檢視值。 在下列範例中，連線狀態會顯示為 [已連線]**，且您可以看見輸入和輸出位元組。

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>對等互連中樞與輪輻虛擬網路

現在對等互連中樞與輪輻虛擬網路。

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>建立路由

接下來，建立幾個路由：

- 透過防火牆 IP 位址，從中樞閘道子網路到輪輻子網路的路由
- 透過防火牆 IP 位址，從輪輻子網路開始的預設路由

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>建立虛擬機器

現在建立輪輻工作負載和內部部署虛擬機器，並將它們放在適當的子網路中。

### <a name="create-the-workload-virtual-machine"></a>建立工作負載虛擬機器

在輪輻虛擬網路中建立虛擬機器，該虛擬機器會執行 IIS、沒有公用 IP 位址，而且允許 Ping 傳入。
出現提示時，請輸入虛擬機器的使用者名稱和密碼。

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>建立內部部署虛擬機器

這是一部簡單的虛擬機器，您可使用遠端桌面連線到公用 IP 位址。 從該處，您可透過防火牆接著連線到內部部署伺服器。 出現提示時，請輸入虛擬機器的使用者名稱和密碼。

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>測試防火牆

首先，取得而後記下 **VM-spoke-01** 虛擬機器的私人 IP 位址。

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

從 Azure 入口網站，連線到 **VM-Onprem** 虛擬機器。
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
在 **VM-Onprem** 上開啟網頁瀏覽器，並瀏覽至 http://\<VM-spoke-01 private IP\>。

您應該會看到 Internet Information Services 預設頁面。

從 **VM-Onprem**，針對位於私人 IP 位址的 **VM-spoke-01** 開啟遠端桌面。

您的連線應會成功，而且應該能夠使用您所選的使用者名稱和密碼登入。

因此，現在您已確認防火牆規則正在運作：

<!---- You can ping the server on the spoke VNet.--->
- 您可以瀏覽輪輻虛擬網路上的網頁伺服器。
- 您可以使用 RDP 連線到輪輻虛擬網路上的伺服器。

接下來，將防火牆網路規則集合動作變更為 [拒絕]****，確認防火牆規則會如預期般運作。 請執行下列指令碼，將規則集合動作變更為 [拒絕]****。

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

現在再次執行測試。 這次所有測試應該都會失敗。 在測試已變更的規則之前，請關閉任何現有的遠端桌面。

## <a name="clean-up-resources"></a>清除資源

您可以保留防火牆資源供下一個教學課程使用，若不再需要，則可刪除 **FW-Hybrid-Test** 資源群組來刪除所有防火牆相關資源。

## <a name="next-steps"></a>後續步驟

接下來，您可以監視 Azure 防火牆記錄。

[教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)
