---
title: 與內部負載等化器一起使用 - Azure 應用程式閘道
description: 本頁面提供使用 Azure 資源管理員建立、設定、啟動、刪除搭配內部負載平衡器 (ILB) 的 Azure 應用程式閘道的指示。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 9fbde84c499ba5b086ce812de63602c626b416b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74179323"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>利用內部負載平衡器 (ILB) 建立應用程式閘道

可以使用網際網路對向的 VIP 或不會對網際網路公開的內部端點 (也稱為內部負載平衡器 (ILB) 端點) 來設定 Azure 應用程式閘道。 使用 ILB 設定閘道適合不會對網際網路公開的內部企業營運應用程式。 對於位在不會對網際網路公開的安全性界限中的多層式應用程式內的服務/階層也十分實用，但仍需要循環配置資源負載散發、工作階段綁定或安全通訊端層 (SSL) 終止。

本文會逐步引導您完成使用 ILB 設定應用程式閘道。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 按照[安裝說明](/powershell/azure/install-az-ps)安裝最新版本的 Azure PowerShell 模組。
2. 您會建立應用程式閘道的虛擬網路和子網路。 請確定沒有虛擬機器或是雲端部署正在使用子網路。 應用程式閘道必須單獨在虛擬網路子網路中。
3. 您要設定來使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是已指派公用 IP/VIP。

## <a name="what-is-required-to-create-an-application-gateway"></a>建立應用程式閘道需要什麼？

* **後端伺服器集區：** 後端伺服器的 IP 位址清單。 列出的 IP 位址應屬於虛擬網路，但在應用程式閘道的不同子網路中，或應該是公用 IP/VIP。
* **後端伺服器池設置：** 每個池都有埠、協定和基於 Cookie 的關聯等設置。 這些設定會繫結至集區，並套用至集區內所有伺服器。
* **前端連接埠：** 此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會達到此連接埠，然後重新導向至其中一個後端伺服器。
* **接聽程式：** 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
* **規則：** 規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。 目前只支援 *基本* 規則。 *基本* 規則是循環配置資源的負載分配。

## <a name="create-an-application-gateway"></a>建立應用程式閘道

使用「Azure 傳統」和「Azure Resource Manager」的差別，在於您建立應用程式閘道和需設定項目的順序。
透過 Resource Manager，組成應用程式閘道的所有項目會個別進行設定，然後一併建立應用程式閘道資源。

以下是建立應用程式閘道所需的步驟：

1. 建立資源管理員的資源群組
2. 建立應用程式閘道的虛擬網路和子網路
3. 建立應用程式閘道組態物件
4. 建立應用程式閘道資源

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源管理員的資源群組

請確定您切換 PowerShell 模式以使用 Azure 資源管理員 Cmdlet。 有關詳細資訊，請參閱使用[Windows PowerShell 與資源管理器](../powershell-azure-resource-manager.md)。

### <a name="step-1"></a>步驟 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶。

```powershell
Get-AzSubscription
```

系統會提示使用您的認證進行驗證。

### <a name="step-3"></a>步驟 3

選擇其中一個要使用的 Azure 訂用帳戶。

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>步驟 4

建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Azure 資源管理員需要所有的資源群組指定一個位置。 這用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 "appgw-rg" 的資源群組，且位置為美國西部 ("West US")。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立應用程式閘道的虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路：

### <a name="step-1"></a>步驟 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

這個步驟會將位址範圍 10.0.0.0/24 指派給用於建立虛擬網路的子網路變數。

### <a name="step-2"></a>步驟 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

這個步驟會使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24，在美國西部 ("West US") 區域的 "appgw-rg" 資源群組中建立名為 "appgwvnet" 的虛擬網路。

### <a name="step-3"></a>步驟 3

```powershell
$subnet = $vnet.subnets[0]
```

這個步驟會將子網路物件指派給下一個步驟的變數 $subnet。

## <a name="create-an-application-gateway-configuration-object"></a>建立應用程式閘道組態物件

### <a name="step-1"></a>步驟 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

這個步驟會建立名為 "gatewayIP01" 的應用程式閘道 IP 組態。 當應用程式閘道啟動時，它會從設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

### <a name="step-2"></a>步驟 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

這個步驟會設定名為 "pool01" 的後端 IP 位址集區，其 IP 位址有 "10.1.1.8、10.1.1.9、10.1.1.10"。 這些 IP 位址會接收來自前端 IP 端點的網路流量。 您可取代上述 IP 位址來新增自己的應用程式 IP 位址端點。

### <a name="step-3"></a>步驟 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

這個步驟會設定後端集區中負載平衡網路流量的應用程式閘道設定 "poolsetting01"。

### <a name="step-4"></a>步驟 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

這個步驟會設定 ILB 的前端 IP 連接埠，名為 "frontendport01"。

### <a name="step-5"></a>步驟 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

這個步驟會建立名為 "fipconfig01" 的前端 IP 組態，並與目前虛擬網路子網路的私人 IP 產生關聯。

### <a name="step-6"></a>步驟 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

這個步驟會建立名為 "listener01" 的接聽程式，並將前端連接埠與前端 IP 組態產生關聯。

### <a name="step-7"></a>步驟 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

這個步驟會建立名為 "rule01" 的負載平衡器路由規則，設定負載平衡器的行為。

### <a name="step-8"></a>步驟 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

這個步驟會設定應用程式閘道的執行個體大小。

> [!NOTE]
> Capacity 的預設值是 2。 針對 Sku 名稱，您可以選擇 Standard_Small、Standard_Medium 或 Standard_Large。

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>使用 New-AzureApplicationGateway 建立應用程式閘道

利用上述步驟中的所有組態項目來建立應用程式閘道。 此範例中的應用程式閘道名為 "appgwtest"。

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

這個步驟會利用上述步驟中的所有組態項目來建立應用程式閘道。 範例中的應用程式閘道名為 "appgwtest"。

## <a name="delete-an-application-gateway"></a>刪除應用程式閘道

若要刪除應用程式閘道，您需要依序執行下列步驟：

1. 使用 `Stop-AzApplicationGateway` Cmdlet 停止閘道。
2. 使用 `Remove-AzApplicationGateway` Cmdlet 移除閘道。
3. 使用 `Get-AzureApplicationGateway` Cmdlet 確認已移除閘道。

### <a name="step-1"></a>步驟 1

取得應用程式閘道物件，並關聯至變數 "$getgw"。

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>步驟 2

使用 `Stop-AzApplicationGateway` 停止應用程式閘道。 這個範例的第一行顯示 `Stop-AzApplicationGateway` Cmdlet，後面接著輸出。

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

當應用程式閘道處於已停止狀態之後，使用 `Remove-AzApplicationGateway` Cmdlet 來移除服務。

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> **-force** 參數可用來隱藏移除確認訊息。

若要確認已移除服務，您可以使用 `Get-AzApplicationGateway` Cmdlet。 這不是必要步驟。

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>後續步驟

如果您想要設定 SSL 卸載，請參閱 [設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想進一步了解一般負載平衡選項，請參閱：

* [Azure 負載等化器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理器](https://azure.microsoft.com/documentation/services/traffic-manager/)

