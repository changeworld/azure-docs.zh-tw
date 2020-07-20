---
title: 使用 Azure 應用程式閘道來設定端對端 TLS
description: 本文說明如何透過 PowerShell 使用 Azure 應用程式閘道來設定端對端 SSL
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: e35569a1dc5ce7c1cb2889ac3e2ca8f60f8ccd42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808202"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>透過 PowerShell 使用應用程式閘道來設定端對端 SSL

## <a name="overview"></a>概觀

Azure 應用程式閘道支援為流量進行端對端加密。 「應用程式閘道」會終止應用程式閘道上的 TLS/SSL 連線。 閘道接著會對流量套用路由規則、重新加密封包，並根據所定義的路由規則將封包轉送至適當的後端伺服器。 任何來自 Web 伺服器的回應都會經歷相同的程序而回到使用者端。

應用程式閘道支援定義自訂 TLS 選項。 也支援停用下列通訊協定版本：**TLSv1.0**、**TLSv1.1** 和 **TLSv1.2**，也會定義要使用哪個加密套件以及偏好的順序。 若要深入了解可設定的 TLS 選項，請參閱 [TLS 原則概觀](application-gateway-SSL-policy-overview.md)。

> [!NOTE]
> 預設會停用 SSL 2.0 和 SSL 3.0，並且無法啟用。 這些版本被認為並不安全，因此不能搭配應用程式閘道使用。

![案例影像][scenario]

## <a name="scenario"></a>狀況

在此案例中，您將了解如何透過 PowerShell 使用端對端 TLS 建立應用程式閘道。

此案例將會：

* 建立名為 **appgw-rg** 的資源群組。
* 建立名為 **appgwvnet** 且具有 **10.0.0.0/16** 位址空間的虛擬網路。
* 建立名為 **appgwsubnet** 和 **appsubnet** 的兩個子網路。
* 建立支援端對端 TLS 加密的小型應用程式閘道，以限制 TLS 通訊協定版本和加密套件。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要對應用程式閘道設定端對端 TLS，閘道需要憑證，後端伺服器也需要憑證。 閘道憑證用來根據 TLS 通訊協定規格，以衍生對稱金鑰。 接下來，對稱金鑰可以用來加密和解密傳送至閘道的流量。 閘道憑證必須採用「個人資訊交換」(PFX) 格式。 此檔案格式可允許將私密金鑰匯出，而應用程式閘道需要這個匯出的金鑰來執行流量的加密和解密。

若要使用端對端 TLS 加密，應用程式閘道必須明確允許後端。 將後端伺服器的公開憑證上傳至應用程式閘道。 新增憑證可確保應用程式閘道只會與已知的後端執行個體通訊。 這會進而保護端對端通訊。

下列各節將說明設定程序。

## <a name="create-the-resource-group"></a>建立資源群組

本節將引導您建立資源群組，其中包含應用程式閘道。

1. 登入您的 Azure 帳戶。

   ```powershell
   Connect-AzAccount
   ```

2. 選取要用於此案例的訂用帳戶。

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. 建立資源群組。 (如果您使用現有的資源群組，請略過此步驟。)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立應用程式閘道的虛擬網路和子網路

下列範例會建立虛擬網路和兩個子網路。 一個子網路用來保留應用程式閘道。 另一個子網路則用於裝載 Web 應用程式的後端。

1. 指派要用於應用程式閘道的子網路位址範圍。

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > 應該適當地調整針對應用程式閘道所設定的子網路大小。 一個應用程式閘道最多可以針對 10 個執行個體進行設定。 每個執行個體會從子網路取得 1 個 IP 位址。 子網路太小可能會對相應放大應用程式閘道產生負面影響。
   >

2. 指派要用於後端位址集區的位址範圍。

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. 建立具有上述步驟所定義子網路的虛擬網路。

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. 擷取要用於後續步驟的虛擬網路資源和子網路資源。

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

建立要用於應用程式閘道的公用 IP 資源。 此公用 IP 位址會用於下列其中一個後續步驟。

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> 應用程式閘道不支援使用以定義的網域標籤建立的公用 IP 位址。 只支援具有動態建立之網域標籤的公用 IP 位址。 如果您需要讓應用程式閘道具有好記的 DNS 名稱，建議您使用 CNAME 記錄作為別名。

## <a name="create-an-application-gateway-configuration-object"></a>建立應用程式閘道組態物件

所有設定項目是在建立應用程式閘道之前設定。 下列步驟會建立應用程式閘道資源所需的組態項目。

1. 建立應用程式閘道 IP 組態物件。 此設定會設定應用程式閘道使用的子網路。 當應用程式閘道啟動時，它會從已設定的子網路取得 IP 位址，然後將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. 建立前端 IP 組態。 此設定會將私人或公用 IP 位址對應到應用程式閘道的前端。 下一個步驟會將先前步驟中的公用 IP 位址關聯到前端 IP 組態。

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. 使用後端 Web 伺服器的 IP 位址設定後端 IP 位址集區。 這些 IP 位址會接收來自前端 IP 端點的網路流量。 以您自己的應用程式 IP 位址端點取代範例中的 IP 位址。

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > 完整網域名稱 (FQDN) 也是可用來取代後端伺服器 IP 位址的有效值。 您可以使用 **-BackendFqdns** 參數啟用它。 

4. 設定公用 IP 端點的前端 IP 連接埠。 此連接埠是供使用者連接到的連接埠。

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. 設定應用程式閘道憑證。 此憑證可用來解密和重新加密應用程式閘道上的流量。

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > 此範例會設定 TLS 連線所使用的憑證。 憑證必須是 .pfx 格式，而密碼則必須介於 4 到 12 個字元。

6. 建立應用程式閘道的 HTTP 接聽程式。 指派要使用的前端 IP 設定、連接埠和 TLS/SSL 憑證。

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. 上傳要在已啟用 TLS 的後端集區資源上使用的憑證。

   > [!NOTE]
   > 預設探查會從後端 IP 位址上的「預設」TLS 繫結取得公開金鑰，並將所收到的公開金鑰值與您在此處提供的公開金鑰值做比較。 
   > 
   > 如果您在後端上使用主機標頭和伺服器名稱指示 (SNI)，擷取的公開金鑰不一定就是流量要流向的預定網站。 如果不確定，請在後端伺服器上造訪 https://127.0.0.1/ ，以確認哪個憑證用於「預設」TLS 繫結。 請使用來自本節該要求的公開金鑰。 如果您在 HTTPS 繫結上使用主機標頭和 SNI，但在後端伺服器上從瀏覽器手動向 https://127.0.0.1/ 要求時，並未收到回應和憑證，則必須在後端伺服器上設定預設 TLS 繫結。 如果您未這麼做，探查將會失敗，而且不允許後端。
   
   關於應用程式閘道中的 SNI，如需詳細資訊，請參閱[應用程式閘道的 TLS 終止和端對端 TLS 概觀](ssl-overview.md)。

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > 上一個步驟中提供的憑證，應該是後端出現的 .pfx 憑證的公開金鑰。 將安裝在後端伺服器上的憑證 (非根憑證) 以「宣告、證據和推論」(CER) 格式匯出，並將它用於此步驟。 此步驟允許使用應用程式閘道的後端。

   如果您使用「應用程式閘道 v2 SKU」，則請建立受信任的根憑證，而不是建立驗證憑證。 如需詳細資訊，請參閱[應用程式閘道的端對端 TLS 概觀](ssl-overview.md#end-to-end-tls-with-the-v2-sku)：

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. 設定應用程式閘道的後端 HTTP 設定。 將前述步驟中上傳的憑證指派給 HTTP 設定。

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   針對「應用程式閘道 v2 SKU」，請使用下列命令：

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. 建立會設定負載平衡器行為的負載平衡器路由規則。 在此範例中，會建立基本的循環配置資源規則。

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. 設定應用程式閘道的執行個體大小。 可用大小是 **Standard\_Small**、**Standard\_Medium** 和 **Standard\_Large**。  容量的可用值為 **1** 到 **10**。

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > 若要進行測試，可以選擇執行個體計數 1。 請務必了解 SLA 不涵蓋任何低於兩個執行個體的執行個體計數，因此不建議使用。 小型閘道適用於開發測試，不適合在生產環境中使用。

11. 設定要在應用程式閘道上使用的 TLS 原則。 應用程式閘道支援設定最小的 TLS 通訊協定版本。

    下列值是可以定義的通訊協定版本清單：

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    下列範例將最小通訊協定版本設定為 **TLSv1_2**，並且只啟用 **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** 和 **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256**。

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

使用上述所有步驟建立應用程式閘道。 建立閘道的執行過程需要很長一段時間。

針對 V1 SKU，請使用下列命令
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

針對 V2 SKU，請使用下列命令
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>後端憑證過期時套用新的憑證

如果後端憑證已過期，請使用此程序來套用新的憑證。

1. 擷取要更新的應用程式閘道。

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. 從 .cer 檔案新增憑證資源，其中包含憑證的公開金鑰，也可以是為了在應用程式閘道上終止 TLS，而新增至接聽程式的同一個憑證。

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. 將新的驗證憑證物件放入變數中 (TypeName:Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate)。

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. 將新憑證指派給 **BackendHttp** 設定，並透過 $AuthCert 變數來參考憑證。 (指定您想要變更的 HTTP 設定名稱。)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. 將變更認可到應用程式閘道，並將包含的新設定傳遞至 $out 變數。
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>從 HTTP 設定中移除未使用的過期憑證

使用此程序從 HTTP 設定中移除未使用的過期憑證。

1. 擷取要更新的應用程式閘道。

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. 列出您想要移除的驗證憑證名稱。

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. 從應用程式閘道中移除驗證憑證。

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. 認可變更。
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>限制現有應用程式閘道上的 TLS 通訊協定版本

上述步驟會引導您建立支援端對端 TLS 的應用程式，並停用特定的 TLS 通訊協定版本。 下列範例會停用現有應用程式閘道上的某些 TLS 原則。

1. 擷取要更新的應用程式閘道。

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. 定義 TLS 原則。 在下列範例中，會停用 **TLSv1.0** 和 **TLSv1.1**，且只有 **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, and **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** 是允許的加密套件。

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. 最後，更新閘道。 此最後一個步驟是漫長的工作。 完成時，就表示應用程式閘道上已設定端對端 TLS。

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>取得應用程式閘道 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (不易記住)。 為了確保使用者可以叫用應用程式閘道，可使用 CNAME 記錄來指向應用程式閘道的公用端點。 如需詳細資訊，請參閱[在 Azure 中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 

若要設定別名，請使用連結至應用程式閘道的 **PublicIPAddress** 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 使用應用程式閘道的 DNS 名稱所建立的 CNAME 記錄，可將兩個 Web 應用程式指向此 DNS 名稱。 不建議使用 A-records，因為重新啟動應用程式閘道時，VIP 可能會變更。

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

如需透過應用程式閘道使用 Web 應用程式防火牆強化 Web 應用程式安全性的詳細資訊，請參閱 [Web 應用程式防火牆概觀](application-gateway-webapplicationfirewall-overview.md)。

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
