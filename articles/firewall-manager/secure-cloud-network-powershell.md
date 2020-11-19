---
title: 教學課程：使用 Azure PowerShell 保護您的虛擬中樞
description: 本文說明如何在中樞內已啟用 Azure 防火牆的單一區域中建立 Azure 虛擬 WAN。
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: e2bcdf947db60fb50b44abba9d6a224768840b28
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656134"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>教學課程：使用 Azure PowerShell 保護您的虛擬中樞

在本教學課程中，您會在一個區域中建立具有虛擬中樞的虛擬 WAN 執行個體，並在虛擬中樞內部署 Azure 防火牆以保護連線。 在此範例中，您會示範虛擬網路之間的安全連線。 虛擬網路和站對站、點對站或 ExpressRoute 分支之間的流量也會受到虛擬安全中樞的支援。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 部署虛擬 WAN
> * 部署 Azure 防火牆和設定自訂路由
> * 測試連線能力

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- PowerShell 7

   進行本教學課程時，您必須在 PowerShell 7 本機上執行 Azure PowerShell。 若要安裝 PowerShell 7，請參閱 [從 Windows PowerShell 5.1 遷移至 PowerShell 7](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7)。
- Az.Network 3.2.0 版

    如果您有 Az.Network 3.4.0 版或更新版本，則必須降級才能使用本教學課程中的某些命令。 您可以使用命令 `Get-InstalledModule -Name Az.Network` 來檢查 Az.Network 模組的版本。 若要解除安裝 Az.Network 模組，請執行 `Uninstall-Module -name az.network`。 若要安裝 Az.Network 3.2.0 模組，請執行 `Install-Module az.network -RequiredVersion 3.2.0 -force`。

## <a name="sign-in-to-azure"></a>登入 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>初始虛擬 WAN 部署

在第一個步驟中，您需要設定一些變數，並建立資源群組、虛擬 WAN 執行個體和虛擬中樞：

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

建立兩個虛擬網路，並將其連線到中樞作為輪輻：

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

此時，您已具備功能完整的虛擬 WAN，可提供任意連線性。 若要以安全性增強，您必須將 Azure 防火牆部署到每個虛擬中樞。 您可以使用防火牆原則有效率地管理虛擬 WAN 的 Azure 防火牆執行個體。 因此，這個範例中也會建立防火牆原則：

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

您可以選擇啟用從 Azure 防火牆到 Azure 監視器的記錄，但在此範例中，您會使用防火牆記錄來證明流量有通過防火牆：

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>部署 Azure 防火牆和設定自訂路由

您現在已在中樞內擁有 Azure 防火牆，但仍需要修改路由，讓虛擬 WAN 透過防火牆傳送來自虛擬網路和來自分支的流量。 您可以分兩個步驟執行此作業：

1. 設定所有虛擬網路連線 (以及分支連線 (如果有的話)) 以傳播至 `None` 路由表。 此設定的作用是其他虛擬網路和分支不會知道其前置詞，因此不會以其作為路由目的地。
1. 現在您可以在 `Default` 路由表 (預設會將與有虛擬網路和分支相關聯) 中插入靜態路由，以便將所有流量傳送至 Azure 防火牆。

> [!NOTE]
> 這是使用 Azure 防火牆管理員保護來自 Azure 入口網站的連線時所部署的設定

從第一個步驟開始，將您的虛擬網路連線設定為傳播至 `None` 路由表：

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

現在您可以繼續進行第二個步驟，將靜態路由新增至 `Default` 路由表。 在此範例中，您會套用 Azure 防火牆管理員在虛擬 WAN 中保護連線時所產生的預設設定，但您可以變更靜態路由中的前置詞清單，以符合您的特定需求：

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>測試連線能力

現在您已擁有可完全正常運作的安全中樞。 若要測試連線，您需要在連線到中樞的每個輪輻虛擬網路中有一部虛擬機器：

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

防火牆原則中的預設設定是捨棄所有項目。 因此，您必須設定一些規則。 從 DNAT 規則開始，讓測試虛擬機器可透過防火牆的公用 IP 位址進行存取：

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

現在您可以設定一些範例規則。 定義允許 SSH 流量的網路規則，以及允許網際網路存取完整網域名稱 `ifconfig.co` 的應用程式規則。 此 URL 會傳回其在 HTTP 要求中看到的來源 IP 位址：

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

在實際傳送任何流量之前，您可以先檢查虛擬機器的有效路由。 其應該包含從虛擬 WAN (`0.0.0.0/0` 加上 RFC1918) 得知的前置詞，而不是其他輪輻的前置詞：

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

現在產生從一部虛擬機器到另一部虛擬機器的流量，並確認其已在 Azure 防火牆中遭到捨棄。 在下列 SSH 命令中，您必須接受虛擬機器指紋，並提供您在建立虛擬機器時所定義的密碼。 在此範例中，您將會從 spoke1 中的虛擬機器將五個 ICMP 回應要求封包傳送至 spoke2，再加上使用 Linux 公用程式 `nc` 在連接埠 22 上的 TCP 連線嘗試 (使用 `-vz` 旗標時，其只會傳送連線要求並顯示結果)。 您應該會看到 ping 失敗，以及連接埠 22 上的 TCP 連線嘗試成功，因為您先前設定的網路規則允許此動作：

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

您也可以確認網際網路流量。 讓 HTTP 要求透過公用程式 `curl` 進入您在防火牆原則 (`ifconfig.co`) 中允許的 FQDN 應該可行，但是要進入任何其他目的地的 HTTP 要求則應該會失敗 (在此範例中，您使用 `bing.com` 進行測試)：

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

若要確認防火牆會捨棄封包，最簡單的方式是檢查記錄。 由於您已設定 Azure 防火牆將記錄傳送至 Azure 監視器，因此您可以使用 Kusto 查詢語言來擷取 Azure 監視器中的相關記錄：

> [!NOTE]
> 記錄可能需要 1 分鐘左右的時間，才會傳送到 Azure 監視器

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

在先前的命令中，您應該會看到不同的項目：

* 進行了 DNAT 的 SSH 連線
* 輪輻中的 VM 之間已捨棄 ICMP 封包 (10.1.1.4 和 10.1.2.4)
* 輪輻中的 VM 之間已允許 SSH 連線

以下是上述命令所產生的範例輸出：

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

如果您想要查看應用程式規則的記錄 (描述已允許和已拒絕的 HTTP 連線) 或變更記錄的顯示方式，則可以嘗試使用其他 KQL 查詢。 您可以在 [Azure 防火牆的 Azure 監視器記錄](../firewall/log-analytics-samples.md)中找到一些範例。


## <a name="clean-up-resources"></a>清除資源

若要刪除測試環境，您可以移除資源群組以及所有內含物件：

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解信任的安全性合作夥伴](trusted-security-partners.md)