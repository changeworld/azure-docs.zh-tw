---
title: 如何在虛擬網路中使用應用程式閘道的 API 管理
titleSuffix: Azure API Management
description: 了解如何以應用程式閘道 (WAF) 做為前端在內部虛擬網路中安裝和設定 Azure API 管理
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 733f4b74ca7643476586189b36f4e1d3e446968b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80811166"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>整合內部 VNET 中的 API 管理與應用程式閘道

## <a name="overview"></a><a name="overview"> </a>總覽

API 管理服務可以內部模式設定於虛擬網路中，因此只能從虛擬網路中加以存取。 Azure 應用程式閘道是一項 PAAS 服務，可提供第 7 層負載平衡器。 它可做為反向 Proxy 服務，並在其供應項目中提供 Web 應用程式防火牆 (WAF)。

結合使用內部 VNET 中佈建的 API 管理與應用程式閘道前端，可實現下列案例︰

* 使用相同的 API 管理資源供內部取用者和外部取用者取用。
* 使用單一 API 管理資源，並在 API 管理中定義一部分 API 供外部取用者使用。
* 提供周全的方法來開啟和關閉從公用網際網路對 API 管理的存取權。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要執行本文所述的步驟，您必須具有：

* 有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* 憑證 - 適用於 API 主機名稱的 cer 和 pfx，和適用於開發人員入口網站主機名稱的 pfx。

## <a name="scenario"></a><a name="scenario"> </a>案例

本文說明如何針對內部和外部取用者使用單一 API 管理服務，並將其作為內部部署和雲端 Api 的單一前端。 您也會看到如何使用應用程式閘道中提供的路由功能，只公開您 API 的一部分 (在範例中以綠色醒目提示) 供外部取用。

在第一個設定範例中，您所有的 API 只能從虛擬網路內部進行管理。 內部取用者 (以橘色醒目提示) 則可存取所有的內部和外部 API。 流量永遠不會送到網際網路。 透過 Express Route 線路傳遞高效能的連線能力。

![URL 路由](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"> </a>開始之前

* 確定您使用最新版本的 Azure PowerShell。 請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)上的安裝指示。 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>在 API 管理和應用程式閘道之間建立整合的所需條件為何？

* **後端伺服器集區︰** 這是 API 管理服務的內部虛擬 IP 位址。
* **後端伺服器集區設定：** 每個集區都有設定，例如埠、通訊協定和以 cookie 為基礎的親和性。 這些設定會套用至集區內所有伺服器。
* **前端連接埠：** 這是在應用程式閘道上開啟的公用連接埠。 到達的流量會重新導向至其中一個後端伺服器。
* 接聽程式 **：** 接聽程式具有前端埠、通訊協定（Http 或 Https，這些值都區分大小寫）和 TLS/SSL 憑證名稱（如果設定 TLS 卸載）。
* **規則︰** 規則會繫結接聽程式至後端伺服器集區。
* **自訂健全狀況探查︰** 應用程式閘道預設會使用 IP 位址型探查，來找出 BackendAddressPool 中有哪些伺服器正在作用中。 API 管理服務只會回應具有正確主機標頭的要求，因此預設探查會失敗。 需要定義自訂的健全狀況探查以協助應用程式閘道判斷服務正在執行，因此它應該轉送要求。
* **自訂網域憑證︰** 若要從網際網路存取 API 管理，您需要建立其主機名稱和應用程式閘道前端 DNS 名稱的 CNAME 對應。 這可確保主機名稱的標頭和憑證傳送到轉送至 API 管理的應用程式閘道，是 APIM 可以辨識為有效的。 在此範例中，我們將使用兩個憑證 - 分別用於後端和開發人員入口網站。  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"> </a>整合 API 管理和應用程式閘道的所需步驟

1. 建立資源管理員的資源群組。
2. 建立應用程式閘道的虛擬網路、子網路和公用 IP。 為 API 管理建立其他子網路。
3. 在上面所建立的 VNET 子網路內建立 API 管理服務，並確保您使用內部模式。
4. 在 API 管理服務中設定自訂網域名稱。
5. 建立應用程式閘道組態物件。
6. 建立應用程式閘道資源。
7. 從應用程式閘道的公用 DNS 名稱建立 CNAME 到 API 管理 Proxy 主機名稱。

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>透過應用程式閘道在外部公開開發人員入口網站

在本指南中，我們也會透過應用程式閘道對外部對象公開**開發人員入口網站**。 這需要執行額外的步驟，以建立開發人員入口網站的接聽程式、探查、設定和規則。 所有詳細資料分別提供於各自的步驟中。

> [!WARNING]
> 如果您使用 Azure AD 或第三方驗證，請啟用應用程式閘道中的 [cookie 型工作階段同質性](../application-gateway/features.md#session-affinity)功能。

> [!WARNING]
> 若要防止應用程式閘道 WAF 在開發人員入口網站中中斷下載 OpenAPI 規格，您必須停用防火牆規則 `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` 。

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源管理員的資源群組

### <a name="step-1"></a>步驟 1

登入 Azure

```powershell
Connect-AzAccount
```

使用您的認證進行驗證。

### <a name="step-2"></a>步驟 2

選取所需的訂用帳戶。

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>步驟 3

建立資源群組 (若使用現有的資源群組，請略過此步驟)。

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure 資源管理員需要所有的資源群組指定一個位置。 這用來作為該資源群組中資源的預設位置。 請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立應用程式閘道的虛擬網路和子網路

下列範例顯示如何使用 Resource Manager 建立虛擬網路。

### <a name="step-1"></a>步驟 1

對要在建立虛擬網路時用於應用程式閘道的子網路變數指派位址範圍 10.0.0.0/24。

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>步驟 2

對要在建立虛擬網路時用於 API 管理的子網路變數指派位址範圍 10.0.1.0/24。

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>步驟 3

在美國西部區域的 **apim-appGw-RG** 資源群組中建立名為 **appgwvnet** 的虛擬網路。 請使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24 和 10.0.1.0/24。

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>步驟 4

指派子網路變數以供後續步驟使用

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>在以內部模式設定的 VNET 內建立 API 管理服務

下列範例示範如何以僅針對內部存取設定的 VNET 建立 API 管理服務。

### <a name="step-1"></a>步驟 1

使用上面所建立的子網路 $apimsubnetdata 建立 API 管理虛擬網路物件。

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>步驟 2

在虛擬網路內建立 API 管理服務。

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

上述命令執行成功之後，請參閱[要存取內部 VNET API 管理服務所需的 DNS 組態](api-management-using-with-internal-vnet.md#apim-dns-configuration)來存取它。 此步驟可能需耗時半小時以上。

## <a name="set-up-a-custom-domain-name-in-api-management"></a>在 API 管理中設定自訂網域名稱

> [!IMPORTANT]
> 除了下列步驟之外，[新的開發人員入口網站](api-management-howto-developer-portal.md)也需要啟用 API 管理的管理端點連線。

### <a name="step-1"></a>步驟 1

使用具有網域之私密金鑰的憑證詳細資料，初始化下列變數。 在此範例中，我們會使用 `api.contoso.net` 和 `portal.contoso.net`。  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>步驟 2

建立並設定 proxy 和入口網站的主機名稱設定物件。  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> 若要設定舊版開發人員入口網站連線，您需要將取代 `-HostnameType DeveloperPortal` 為 `-HostnameType Portal` 。

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端組態的公用 IP 位址

在資源群組中建立公用 IP 資源 **publicIP01**。

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

在服務啟動時，系統會將 IP 位址指派至應用程式閘道。

## <a name="create-application-gateway-configuration"></a>建立應用程式閘道組態

建立應用程式閘道之前，必須先設定所有組態項目。 下列步驟會建立應用程式閘道資源所需的組態項目。

### <a name="step-1"></a>步驟 1

建立名為**gatewayIP01**的應用程式閘道 IP 設定。 當應用程式閘道啟動時，它會從設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>步驟 2

設定公用 IP 端點的前端 IP 連接埠。 此連接埠是供使用者連接到的連接埠。

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>步驟 3

利用公用 IP 端點設定前端 IP。

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>步驟 4

設定應用程式閘道的憑證，這些憑證將用來解密和重新加密流經的流量。

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>步驟 5

建立應用程式閘道的 HTTP 接聽程式。 將前端 IP 設定、埠和 TLS/SSL 憑證指派給它們。

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>步驟 6

建立 API 管理服務 `ContosoApi` Proxy 網域端點的自訂探查。 `/status-0123456789abcdef` 路徑是裝載於所有 API 管理服務上的預設健全狀況端點。 設定 `api.contoso.net` 為自訂探查主機名稱，以使用 TLS/SSL 憑證來保護它。

> [!NOTE]
> 主機名稱 `contosoapi.azure-api.net` 則是名為 `contosoapi` 的服務在公用 Azure 中建立時所設定的預設 Proxy 主機名稱。
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>步驟 7

上傳要在具備 TLS 功能的後端集區資源上使用的憑證。 此憑證與您在上述步驟 4 中提供的憑證相同。

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>步驟 8

設定應用程式閘道的 HTTP 後端設定。 這包括設定據以取消後端要求的逾時限制。 此值與探查逾時不同。

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>步驟 9

以上面建立的 API 管理服務內部虛擬 IP 位址設定名為 **apimbackend** 的後端 IP 位址集區。

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>步驟 10

建立讓應用程式閘道使用基本路由的規則。

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> 變更 -RuleType 和路由，以限制對特定開發人員入口網站頁面的存取。

### <a name="step-11"></a>步驟 11

設定執行個體數目和應用程式閘道的大小。 在此範例中，我們使用 [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) 以提高 API 管理資源的安全性。

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>步驟 12

將 WAF 設定為「防止」模式。

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>建立應用程式閘道

利用上述步驟中的所有組態物件來建立應用程式閘道。

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>將 API 管理 Proxy 主機名稱 CNAME 到應用程式閘道資源的公用 DNS 名稱

建立閘道之後，下一步是設定通訊的前端。 當使用公用 IP 時，應用程式閘道需要動態指派的 DNS 名稱 (可能不易記住)。

您應該使用應用程式閘道的 DNS 名稱來建立 CNAME 記錄，將 APIM Proxy 主機名稱 (例如，上面範例中的 `api.contoso.net`) 指向此 DNS 名稱。 若要設定前端 IP CNAME 記錄，使用 PublicIPAddress 元素，擷取應用程式閘道的詳細資料及其關聯的 IP/DNS 名稱。 不建議使用 A-records，因為重新啟動閘道時，VIP 可能會變更。

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"> </a>摘要
VNET 中設定的 Azure API 管理為所有已設定的 Api 提供單一閘道介面，不論它們是裝載于內部部署或雲端。 整合應用程式閘道與 API 管理提供選擇性地使特定 API 可在網際網路上存取的彈性，並提供 Web 應用程式防火牆來做為 API 管理執行個體的前端。

## <a name="next-steps"></a><a name="next-steps"> </a>後續步驟
* 深入了解 Azure 應用程式閘道
  * [應用程式閘道總覽](../application-gateway/application-gateway-introduction.md)
  * [應用程式閘道 Web 應用程式防火牆](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [使用路徑型路由的應用程式閘道](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* 深入了解 API 管理和 VNET
  * [使用只在 VNET 內提供的 API 管理](api-management-using-with-internal-vnet.md)
  * [在 VNET 中使用 API 管理](api-management-using-with-vnet.md)
