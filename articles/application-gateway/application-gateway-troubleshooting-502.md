---
title: 針對不正確的閘道錯誤進行疑難排解-Azure 應用程式閘道
description: 瞭解如何針對應用程式閘道 Server 錯誤進行疑難排解： 502-網頁伺服器作為閘道或 proxy 伺服器時收到不正確回應。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 1b0abe998540c4fcc0a9b83f6d1175e18a560871
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808145"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>疑難排解應用程式閘道中閘道不正確的錯誤

瞭解如何疑難排解使用 Azure 應用程式閘道時所收到的錯誤閘道（502）錯誤。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>總覽

設定應用程式閘道之後，您可能會看到的其中一個錯誤是「伺服器錯誤： 502-網頁伺服器作為閘道或 proxy 伺服器時收到不正確回應」。 發生此錯誤的主要原因如下：

* NSG、UDR 或自訂 DNS 封鎖後端集區成員的存取。
* 虛擬機器擴展集的後端 Vm 或實例未回應預設健康情況探查。
* 無效或不適當的自訂健全狀況探查組態。
* Azure 應用程式閘道的[後端集區未設定或空白](#empty-backendaddresspool)。
* [虛擬機器擴展集的虛擬機器或執行個體都不是良好健康情況](#unhealthy-instances-in-backendaddresspool)。
* [要求超時或](#request-time-out)使用者要求的連線問題。

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>網路安全性群組、使用者定義的路由或自訂 DNS 問題

### <a name="cause"></a>原因

如果因為 NSG、UDR 或自訂 DNS 而封鎖對後端的存取，應用程式閘道實例就無法連線到後端集區。 這會導致探查失敗，並產生502錯誤。

NSG/UDR 可能會出現在應用程式閘道子網中，或是應用程式 Vm 部署所在的子網中。

同樣地，VNet 中是否有自訂 DNS 也可能會造成問題。 使用者為 VNet 設定的 DNS 伺服器可能無法正確解析用於後端集區成員的 FQDN。

### <a name="solution"></a>解決方案

透過下列步驟來驗證 NSG、UDR 和 DNS 設定：

* 檢查與應用程式閘道子網相關聯的 Nsg。 確定不會封鎖對後端的通訊。
* 檢查與應用程式閘道子網相關聯的 UDR。 請確定 UDR 不會將流量從後端子網導向出去。 例如，檢查網路虛擬裝置的路由，或透過 ExpressRoute/VPN 向應用程式閘道子網通告的預設路由。

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* 檢查有效的 NSG 和後端虛擬機器的路由

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* 檢查 VNet 中存在自訂 DNS。 您可以在輸出中查看 VNet 屬性的詳細資料來檢查 DNS。

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
如果存在，請確定 DNS 伺服器可正確解析後端集區成員的 FQDN。

## <a name="problems-with-default-health-probe"></a>預設健全狀況探查的問題

### <a name="cause"></a>原因

502錯誤也可能是預設健康情況探查無法連線到後端 Vm 的頻繁指示器。

布建應用程式閘道實例時，它會使用 BackendHttpSetting 的屬性，自動設定每個 BackendAddressPool 的預設健康情況探查。 設定此探查時不需任何使用者輸入。 具體而言，設定負載平衡規則時，會在 BackendHttpSetting 和 BackendAddressPool 之間建立關聯。 預設探查會針對每個關聯設定，而應用程式閘道會在 BackendHttpSetting 元素所指定的埠上，啟動 BackendAddressPool 中每個實例的定期健全狀況檢查連接。 

下表列出與預設健全狀況探查相關聯的值：

| 探查屬性 | 值 | 說明 |
| --- | --- | --- |
| 探查 URL |`http://127.0.0.1/` |URL 路徑 |
| 間隔 |30 |探查間隔 (秒) |
| 逾時 |30 |探查逾時 (秒) |
| 狀況不良臨界值 |3 |探查重試計數。 連續探查失敗計數到達狀況不良臨界值後，就會將後端伺服器標示為故障。 |

### <a name="solution"></a>解決方案

* 確定預設網站已設定且正於 127.0.0.1 上進行接聽。
* 如果 BackendHttpSetting 指定了 80 以外的連接埠，則應將預設網站設定為在該連接埠上進行接聽。
* 對 `http://127.0.0.1:port` 的呼叫應該會傳回 HTTP 結果碼 200。 這應該會在30秒的超時期間內傳回。
* 請確定設定的埠已開啟，而且沒有防火牆規則或 Azure 網路安全性群組，這會封鎖所設定埠上的傳入或傳出流量。
* 如果 Azure 傳統 Vm 或雲端服務與 FQDN 或公用 IP 搭配使用，請確認對應的[端點](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json)已開啟。
* 如果 VM 是透過 Azure Resource Manager 設定，而且位於應用程式閘道部署所在的 VNet 外部，則必須將[網路安全性群組](../virtual-network/security-overview.md)設定為允許在所需的埠上進行存取。

## <a name="problems-with-custom-health-probe"></a>自訂健全狀況探查的問題

### <a name="cause"></a>原因

自訂的健全狀況探查能夠對於預設探查行為提供更多彈性。 當您使用自訂探查時，您可以設定探查間隔、URL、要測試的路徑，以及將後端集區實例標示為狀況不良之前，要接受的失敗回應次數。

新增下列其他屬性：

| 探查屬性 | 說明 |
| --- | --- |
| 名稱 |探查的名稱。 此名稱用來在後端 HTTP 設定中指出探查。 |
| 通訊協定 |用來傳送探查的通訊協定。 探查會使用後端 HTTP 設定中定義的通訊協定 |
| Host |用來傳送探查的主機名稱。 僅適用于在應用程式閘道上設定多網站時。 這與 VM 主機名稱不同。 |
| Path |探查的相對路徑。 有效路徑的開頭為 '/'。 探查會傳送至/ \<protocol\> / \<host\> ：\<port\>\<path\> |
| 間隔 |探查間隔 (秒)。 這是兩個連續探查之間的時間間隔。 |
| 逾時 |探查逾時 (秒)。 如果在這個逾時期間內未收到有效的回應，則會將探查標示為失敗。 |
| 狀況不良臨界值 |探查重試計數。 連續探查失敗計數到達狀況不良臨界值後，就會將後端伺服器標示為故障。 |

### <a name="solution"></a>解決方案

確認已按照上述資料表正確設定 [自訂健全狀態探查]。 除了上述的疑難排解步驟，也請確定下列各項：

* 確定已根據 [指南](application-gateway-create-probe-ps.md)正確指定探查。
* 如果已針對單一網站設定應用程式閘道，則預設應將主機名稱指定為 `127.0.0.1` ，除非在自訂探查中另有設定。
* 確定對 HTTP://：的呼叫會傳回 \<host\> \<port\> \<path\> HTTP 結果碼200。
* 確定 Interval、Timeout 和 UnhealtyThreshold 都在可接受的範圍內。
* 若使用 HTTPS 探查，請在後端伺服器本身設定後援憑證，以確定後端伺服器不會要求您提供 SNI。

## <a name="request-time-out"></a>要求逾時

### <a name="cause"></a>原因

收到使用者要求時，應用程式閘道會將已設定的規則套用至要求，並將其路由傳送至後端集區實例。 其會等候一段可設定的時間間隔以接收來自後端應用程式的回應。 根據預設，此間隔為**20**秒。 如果應用程式閘道未在此間隔內收到來自後端應用程式的回應，則使用者要求會得到502錯誤。

### <a name="solution"></a>解決方案

應用程式閘道可讓您透過 BackendHttpSetting 設定此設定，然後將它套用至不同的集區。 不同的後端集區可以有不同的 BackendHttpSetting，並已設定不同的要求超時。

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>空白的 BackendAddressPool

### <a name="cause"></a>原因

如果應用程式閘道沒有在後端位址集區中設定的 Vm 或虛擬機器擴展集，它就無法路由傳送任何客戶要求，也不會送出錯誤的閘道錯誤。

### <a name="solution"></a>解決方案

請確定後端位址集區不是空的。 這可透過 PowerShell、CLI 或入口網站來完成。

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

前述 Cmdlet 的輸出應包含非空白的後端位址集區。 下列範例顯示針對後端 Vm 使用 FQDN 或 IP 位址設定的兩個集區。 BackendAddressPool 的佈建狀態必須是 'Succeeded'。

BackendAddressPoolsText：

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>BackendAddressPool 中狀況不良的執行個體

### <a name="cause"></a>原因

如果 BackendAddressPool 的所有實例都狀況不良，則應用程式閘道不會有任何後端將使用者要求路由傳送至。 當後端實例狀況良好，但未部署必要的應用程式時，也可能會發生這種情況。

### <a name="solution"></a>解決方案

確定執行個體的狀況良好且已正確設定應用程式。 檢查後端實例是否可以從相同 VNet 中的另一個 VM 回應 ping。 如果設定了公用端點，請確定對 web 應用程式的瀏覽器要求可供維修。

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟[支援票證](https://azure.microsoft.com/support/options/)。

