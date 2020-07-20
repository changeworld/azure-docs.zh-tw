---
title: 使用 PowerShell 建立自訂探查
titleSuffix: Azure Application Gateway
description: 了解如何在資源管理員中使用 PowerShell 建立應用程式閘道的自訂探查
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 2a269137508feb8a2d2cc0710986e1b74b91c0ea
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186736"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>使用 Azure 資源管理員的 PowerShell 建立 Azure 應用程式閘道的自訂探查

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 傳統 PowerShell](application-gateway-create-probe-classic-ps.md)

在本文中，您會使用 PowerShell 將自訂探查新增到現有的應用程式閘道。 對於具有特定健康狀態檢查頁面的應用程式，或是在預設 Web 應用程式上不提供成功回應的應用程式，自訂探查非常實用。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>建立含有自訂探查的應用程式閘道

### <a name="sign-in-and-create-resource-group"></a>登入並建立資源群組

1. 使用 `Connect-AzAccount`來驗證。

   ```powershell
   Connect-AzAccount
   ```

1. 取得帳戶的訂用帳戶。

   ```powershell
   Get-AzSubscription
   ```

1. 選擇其中一個要使用的 Azure 訂用帳戶。

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. 建立資源群組。 如果您有現有的資源群組，則可略過此步驟。

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Azure 資源管理員需要所有的資源群組指定一個位置。 此位置用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 **appgw-RG** 的資源群組，位置為**美國西部**。

### <a name="create-a-virtual-network-and-a-subnet"></a>建立虛擬網路和子網路

下列範例會建立應用程式閘道的虛擬網路和子網路。 「應用程式閘道」需要使用其專屬的子網路。 基於這個理由，針對應用程式閘道建立的子網路應該小於 VNET 位址空間，以便能建立和使用其他的子網路。

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

在「美國西部」區域的資源群組**為 appgw-rg-rg**中建立公用 IP 資源**群組 publicip01** 。 此範例使用公用 IP 位址做為應用程式閘道的前端 IP 位址。  應用程式閘道需要公用 IP 位址才能具有動態建立的 DNS 名稱，因此在公用 IP 位址建立期間無法指定 `-DomainNameLabel`。

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>建立應用程式閘道

您先設定所有組態項目，再建立應用程式閘道。 下列範例會建立應用程式閘道資源所需的組態項目。

| **元件** | **說明** |
|---|---|
| **閘道 IP 設定** | 應用程式閘道的 IP 設定。|
| **後端集區** | 這是應用程式伺服器的 IP 位址、FQDN 或 NIC 的集區，此應用程式伺服器負責裝載 Web 應用程式|
| **健康狀態探查** | 用於監視後端集區成員健康狀態的自訂探查|
| **HTTP 設定** | 包括連接埠、通訊協定、以 Cookie 為依據的親和性、探查和逾時等設定的集合。  這些設定會決定流量路由傳送到後端集區成員的方式|
| **前端連接埠** | 應用程式閘道接聽流量的連接埠|
| **接聽程式** | 通訊協定、前端 IP 設定以及前端連接埠的組合。 這是用於接聽傳入要求的項目。
|**規則**| 根據 HTTP 設定，將流量路由傳送至適當的後端。|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>將探查新增至現有應用程式閘道

下列的程式碼片段會將探查新增至現有的應用程式閘道。

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>從現有應用程式閘道中移除探查

下列的程式碼片段會從現有的應用程式閘道移除探查。

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 [在 Azure 中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 做法是使用連接至應用程式閘道的 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 應用程式閘道的 DNS 名稱應該用來建立將兩個 Web 應用程式指向此 DNS 名稱的 CNAME 記錄。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>後續步驟

若要瞭解如何設定 TLS 卸載，請造訪：[設定 tls](application-gateway-ssl-arm.md)卸載

