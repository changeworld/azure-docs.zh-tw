---
title: 排除錯誤閘道錯誤 - Azure 應用程式閘道
description: 瞭解如何排除應用程式閘道伺服器錯誤：502 - Web 服務器在充當閘道或代理伺服器時收到無效回應。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130481"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>疑難排解應用程式閘道中閘道不正確的錯誤

瞭解如何解決使用 Azure 應用程式閘道時收到的錯誤閘道 （502） 錯誤。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>總覽

配置應用程式閘道後，您可能會看到一個錯誤是"伺服器錯誤：502 - Web 服務器在充當閘道或代理伺服器時收到無效回應"。 發生此錯誤的原因如下：

* NSG、UDR 或自訂 DNS 正在阻止對後端池成員的訪問。
* 後端 VM 或虛擬機器規模集的實例未回應預設運行狀況探測。
* 無效或不適當的自訂健全狀況探查組態。
* Azure 應用程式閘道的[後端池未配置或清空](#empty-backendaddresspool)。
* [虛擬機器擴展集的虛擬機器或執行個體都不是良好健康情況](#unhealthy-instances-in-backendaddresspool)。
* [請求超時或使用者請求的連接問題](#request-time-out)。

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>網路安全性群組、使用者定義的路由或自訂 DNS 問題

### <a name="cause"></a>原因

如果由於 NSG、UDR 或自訂 DNS 而阻止對後端的訪問，則應用程式閘道實例無法訪問後端池。 這將導致探測失敗，導致 502 個錯誤。

NSG/UDR 可以存在於應用程式閘道子網或部署應用程式 VM 的子網中。

同樣，VNet 中存在自訂 DNS 也可能導致問題。 用於後端池成員的 FQDN 可能無法由 VNet 的使用者配置的 DNS 伺服器正確解析。

### <a name="solution"></a>解決方法

透過下列步驟來驗證 NSG、UDR 和 DNS 設定：

* 檢查與應用程式閘道子閘道聯的 NSG。 確保後端的通信未被阻止。
* 檢查與應用程式閘道子閘道聯的 UDR。 確保 UDR 不會將流量從後端子網定向到。 例如，檢查路由到網路虛擬裝置或預設路由通過 ExpressRoute/VPN 通告到應用程式閘道子網。

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
如果存在，請確保 DNS 伺服器可以正確解析後端池成員的 FQDN。

## <a name="problems-with-default-health-probe"></a>預設健全狀況探查的問題

### <a name="cause"></a>原因

502 錯誤也可以是預設運行狀況探測無法到達後端 VM 的頻繁指示器。

預配應用程式閘道實例時，它使用後端Http設置的屬性自動將預設運行狀況探測配置為每個後端位址集區。 設定此探查時不需任何使用者輸入。 具體而言，配置負載平衡規則時，後端Http設置和後端位址集區之間將建立關聯。 為每個關聯配置預設探測，應用程式閘道在後端Http設置元素中指定的埠的後端位址集區中啟動到每個實例的定期運行狀況檢查連接。 

下表列出了與預設運行狀況探測關聯的值：

| 探查屬性 | 值 | 描述 |
| --- | --- | --- |
| 探查 URL |`http://127.0.0.1/` |URL 路徑 |
| 間隔 |30 |探查間隔 (秒) |
| 逾時 |30 |探查逾時 (秒) |
| 狀況不良臨界值 |3 |探查重試計數。 連續探查失敗計數到達狀況不良臨界值後，就會將後端伺服器標示為故障。 |

### <a name="solution"></a>解決方法

* 確定預設網站已設定且正於 127.0.0.1 上進行接聽。
* 如果 BackendHttpSetting 指定了 80 以外的連接埠，則應將預設網站設定為在該連接埠上進行接聽。
* 對 `http://127.0.0.1:port` 的呼叫應該會傳回 HTTP 結果碼 200。 應在 30 秒超時期間返回。
* 確保配置的埠處於打開狀態，並且沒有防火牆規則或 Azure 網路安全性群組，這些防火牆規則或 Azure 網路安全性群組阻止配置的埠上的傳入或傳出流量。
* 如果 Azure 經典 VM 或雲服務與 FQDN 或公共 IP 一起使用，請確保打開相應的[終結點](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json)。
* 如果 VM 是通過 Azure 資源管理器配置的，並且位於部署應用程式閘道的 VNet 之外，則必須將[網路安全性群組](../virtual-network/security-overview.md)配置為允許在所需的埠上進行訪問。

## <a name="problems-with-custom-health-probe"></a>自訂健全狀況探查的問題

### <a name="cause"></a>原因

自訂的健全狀況探查能夠對於預設探查行為提供更多彈性。 使用自訂探測時，可以將探測間隔、URL、要測試的路徑以及要接受的回應失敗數配置為不健康飲食。

添加了以下其他屬性：

| 探查屬性 | 描述 |
| --- | --- |
| 名稱 |探查的名稱。 此名稱用來在後端 HTTP 設定中指出探查。 |
| 通訊協定 |用來傳送探查的通訊協定。 探查會使用後端 HTTP 設定中定義的通訊協定 |
| Host |用來傳送探查的主機名稱。 僅適用于在應用程式閘道上配置多網站時。 這與 VM 主機名稱不同。 |
| Path |探查的相對路徑。 有效路徑的開頭為 '/'。 探查會傳送到 \<通訊協定\>://\<主機\>:\<連接埠\>\<路徑\> |
| 間隔 |探查間隔 (秒)。 這是兩個連續探查之間的時間間隔。 |
| 逾時 |探查逾時 (秒)。 如果在此超時期間未收到有效的回應，則探測器將標記為失敗。 |
| 狀況不良臨界值 |探查重試計數。 連續探查失敗計數到達狀況不良臨界值後，就會將後端伺服器標示為故障。 |

### <a name="solution"></a>解決方法

確認已按照上述資料表正確設定 [自訂健全狀態探查]。 除了上述的疑難排解步驟，也請確定下列各項：

* 確定已根據 [指南](application-gateway-create-probe-ps.md)正確指定探查。
* 如果應用程式閘道配置為單個網站，則預設情況下，主機名稱稱應指定為`127.0.0.1`，除非在自訂探測中另行配置。
* 確定對 http://\<主機\>:\<連接埠\>\<路徑\> 的呼叫會傳回 HTTP 結果碼 200。
* 確保間隔、超時和取消修復閾值在可接受的範圍內。
* 若使用 HTTPS 探查，請在後端伺服器本身設定後援憑證，以確定後端伺服器不會要求您提供 SNI。

## <a name="request-time-out"></a>要求逾時

### <a name="cause"></a>原因

收到使用者請求時，應用程式閘道會將配置的規則應用於請求並將其路由到後端池實例。 其會等候一段可設定的時間間隔以接收來自後端應用程式的回應。 預設情況下，此間隔為**20**秒。 如果應用程式閘道在此間隔內未收到來自後端應用程式的回應，則使用者請求將出現 502 錯誤。

### <a name="solution"></a>解決方法

應用程式閘道允許您通過後端Http設置配置此設置，然後可以將其應用於不同的池。 不同的後端池可以有不同的後端Http設置，並配置不同的請求超時。

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>空白的 BackendAddressPool

### <a name="cause"></a>原因

如果應用程式閘道在後端位址集區中沒有配置 VM 或虛擬機器規模集，則無法路由任何客戶請求併發送錯誤的閘道錯誤。

### <a name="solution"></a>解決方法

確保後端位址集區不為空。 這可透過 PowerShell、CLI 或入口網站來完成。

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

前述 Cmdlet 的輸出應包含非空白的後端位址集區。 下面的示例顯示返回的兩個池，它們配置了 FQDN 或後端 VM 的 IP 位址。 BackendAddressPool 的佈建狀態必須是 'Succeeded'。

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

如果後端位址集區的所有實例都不正常，則應用程式閘道沒有任何後端將使用者請求路由到。 當後端實例正常但未部署所需的應用程式時，也會出現這種情況。

### <a name="solution"></a>解決方法

確定執行個體的狀況良好且已正確設定應用程式。 檢查後端實例是否可以回應來自同一 VNet 中另一個 VM 的 ping。 如果使用公共終結點進行配置，請確保對 Web 應用程式的瀏覽器請求是可維修的。

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，則打開[支援票證](https://azure.microsoft.com/support/options/)。

