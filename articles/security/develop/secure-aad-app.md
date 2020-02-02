---
title: 開發安全的 Azure AD Web 應用程式 |Microsoft Docs
description: 這個簡單的範例應用程式會在您于 Azure 上開發時，實行可改善應用程式和組織安全性狀態的安全性最佳作法。
keywords: NA
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 11bf7c0ae05c2e52d59efb32be47ce6bd96fac4f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937970"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>開發 Azure AD 應用程式的安全應用程式
## <a name="overview"></a>概觀

這個範例是 web 應用程式的簡單 Azure Active Directory，可連結至在 Azure 上開發應用程式的安全性資源。 當您在 Azure 上開發應用程式時，應用程式會實行安全性最佳作法，協助改善您的應用程式和組織的安全性狀態。

部署腳本會設定基礎結構。 執行部署腳本之後，您必須在 Azure 入口網站中進行一些手動設定，以將元件和服務連結在一起。 此範例的目標是 Azure 上有經驗的開發人員，他們在零售業中工作，並想要使用安全的 Azure 基礎結構建立安全的 Azure Active Directory。 


在開發和部署此應用程式時，您將瞭解如何 
- 建立 Azure Key Vault 實例、儲存並從中抓取秘密。
- 部署 Azure Web 應用程式，其專門與前端防火牆存取隔離。 
- 使用 OWASP 前10大規則集的防火牆建立並設定 Azure 應用程式閘道實例。 
- 使用 Azure 服務，啟用傳輸中和待用資料的加密。 
- 設定 Azure 原則和安全性中心以評估 compliancies。 

在您開發並部署此應用程式之後，您將會設定下列範例 web 應用程式，以及所述的設定和安全性量值。

## <a name="architecture"></a>架構
應用程式是典型的多層式應用程式，具有三個層級。 整合了監視和密碼管理元件的前端、後端和資料庫層，如下所示：

![應用程式架構](./media/secure-aad-app/architecture.png)

此解決方案會使用下列 Azure 服務。 部署架構的詳細資料位於部署架構一節中。 

架構是由這些元件所組成

- [Azure 應用程式閘道](../../application-gateway/index.yml)。 提供應用程式架構的閘道和防火牆。
- [Application Insights](../../azure-monitor/app/app-insights-overview.md)。 在多個平臺上提供可擴充的應用程式效能管理（APM）服務。
- [Azure Key Vault](../../key-vault/index.yml)。 會儲存並加密我們的應用程式密碼，並管理其周圍的存取原則建立。
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)。提供雲端式身分識別和存取管理服務、登入和存取資源。
- [Azure 網域名稱系統](../../dns/dns-overview.md)。 提供服務來裝載網域。
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)。 提供調整您的應用程式，並為您的服務建立高可用性。
- [Azure Web 應用程式](../../app-service/overview.md)。  提供用來裝載 web 應用程式的 HTTP 型服務。
- [Azure 資訊安全中心](../../security-center/index.yml)。 針對雲端中的混合式工作負載提供先進的威脅防護。
- [Azure 原則](../../governance/policy/overview.md)。 提供評估您的資源，以符合指派的原則。

## <a name="threat-model"></a>威脅模型
威脅模型化是識別企業和應用程式潛在安全性威脅的程式，然後確保適當的風險降低計畫已就緒。

這個範例使用[Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)來執行安全範例應用程式的威脅模型化。 藉由將元件和資料流程的圖表化，您可以及早識別開發程式中的問題和威脅。 稍後會使用此來儲存時間和金錢。

以下是範例應用程式的威脅模型

![威脅模型](./media/secure-aad-app/threat-model.png)

下列螢幕擷取畫面顯示威脅模型化工具所產生的一些範例威脅和潛在弱點。 威脅模型概述公開的攻擊面，並提示開發人員思考如何減輕問題。

![威脅模型輸出](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>必要條件
若要讓應用程式啟動並執行，您需要安裝下列工具：

- 用來修改和查看應用程式程式碼的程式碼編輯器。[Visual Studio Code](https://code.visualstudio.com/)是一個開放原始碼選項。
- 在您的開發電腦上[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) 。
- 系統上的[Git](https://git-scm.com/) 。 Git 是用來在本機複製原始程式碼。
- [jq](https://stedolan.github.io/jq/)，這是用來以方便使用的方式查詢 JSON 的 UNIX 工具。

您需要 Azure 訂用帳戶，才能部署範例應用程式的資源。 如果您沒有 Azure 訂用帳戶，您可以[建立免費帳戶](https://azure.microsoft.com/free/)來測試範例應用程式。

安裝這些工具之後，您就可以開始在 Azure 上部署應用程式。

### <a name="implementation-guidance"></a>實作指南
部署腳本是一個可以細分成四個階段的腳本。 每個階段都會部署並設定[架構圖](#architecture)中的 Azure 資源。

這四個階段是

- 部署 Azure Key Vault。
- 部署 Azure Web Apps。
- 使用 web 應用程式防火牆部署應用程式閘道。
- 使用已部署的應用程式設定 Azure AD。

每個階段都是使用先前部署的資源中的設定，以前面的一項為基礎。

若要完成執行步驟，請確定您已安裝 [[必要條件](#prerequisites)] 底下列出的工具。

#### <a name="deploy-azure-key-vault"></a>部署 Azure Key Vault
在本節中，您會建立及部署用來儲存秘密和憑證的 Azure Key Vault 實例。

完成部署之後，您會有一個部署在 Azure 上的 Azure Key Vault 實例。

使用 Powershell 部署 Azure Key Vault
 
1. 宣告 Azure Key Vault 的變數。
2. 註冊 Azure Key Vault 提供者。
3. 建立實例的資源群組。
4. 在步驟3中建立的資源群組中建立 Azure Key Vault 實例。

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>下列 Azure AD 使用者將具有 Key Vault 的系統管理員許可權
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>註冊 Az 提供者
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>建立 Azure Key Vault 實例
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>將系統管理員原則新增至 Key Vault
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>建立存取原則，以允許使用者取得和列出密碼編譯金鑰、憑證和密碼（如果您知道使用者主體名稱）：
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

最佳做法是在使用 Key Vault 來存取資源的應用程式中，使用適用于 Azure 資源的受控識別。 當 Key Vault 的存取金鑰不是以程式碼或在設定中儲存時，您的安全性狀態就會增加。

容器中包含根憑證。 取得憑證所採取的步驟包括

1. 從[憑證授權單位](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)單位下載憑證檔案。
2. 將憑證檔案解碼：

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
此腳本會為 App Service 實例建立指派的身分識別，以便與 MSI 搭配使用以與 Azure Key Vault 進行互動，而不需要在程式碼或設定中硬編碼密碼。

移至入口網站中的 Azure Key Vault 實例，在 [存取原則] 索引標籤上授權指派的身分識別。選取 [**新增存取原則**]。 在 [**選取主體**] 底下，搜尋類似于所建立 App Service 實例名稱的應用程式名稱。
附加至應用程式的服務主體應該是可見的。 選取它並 [儲存存取原則] 頁面，如下列螢幕擷取畫面所示。

因為應用程式只需要取得金鑰，請選取 [密碼] 選項中的 [**取得**] 許可權，以允許存取，同時降低授與的許可權。

![Key Vault 存取原則](./media/secure-aad-app/kv-access-policy.png)

*建立 Key Vault 存取原則*

儲存存取原則，然後在 [**存取原則**] 索引標籤上儲存新的變更，以更新原則。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>部署已啟用 web 應用程式防火牆的應用程式閘道
在 web 應用程式中，不建議您直接將服務公開給網際網路上的外部世界。
負載平衡和防火牆規則可讓您更安全地控制連入流量，並協助您進行管理。

若要部署應用程式閘道實例

1. 建立資源群組以存放應用程式閘道。
2. 布建要附加至閘道的虛擬網路。
3. 為虛擬網路中的閘道建立子網。
4. 布建公用 IP 位址。
5. 布建應用程式閘道。
6. 在閘道上啟用 web 應用程式防火牆。

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>部署 Azure Web Apps
Azure App Service 可讓您使用 Python、Ruby、 C#和 JAVA 等語言來建立和裝載 web 應用程式。 Azure 也支援自訂容器，可讓幾乎所有程式設計語言都能在 Azure App Service 平臺上執行。

#### <a name="create-an-app-service-plan-in-free-tier"></a>在免費層中建立 App Service 方案
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>建立 Web 應用程式
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>繼續之前，請移至您的自訂網域的 Azure 網域名稱系統設定 UI，並遵循 https://aka.ms/appservicecustomdns 的指示來設定主機名稱 "www" 的 CNAME 記錄，並將它指向 web 應用程式的預設功能變數名稱

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>將 App Service 規劃升級至共用層（自訂網域的最低需求）
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>將自訂功能變數名稱新增至 web 應用程式
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>指引與建議

### <a name="network"></a>網路
完成部署之後，您會有已啟用 web 應用程式防火牆的應用程式閘道。

閘道實例會針對 HTTPS 公開端口443。 此設定可確保我們的應用程式只能透過 HTTPS 在埠443上存取。

封鎖未使用的埠並限制受攻擊面的風險，是安全性最佳作法。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>將網路安全性群組新增至 App Service 實例

App Service 實例可以與虛擬網路整合。 此整合可讓您設定網路安全性群組原則，以管理應用程式的傳入和傳出流量。

1. 若要啟用這項功能，請在 [Azure App 服務實例] 分頁的 [**設定**] 底下，選取 [**網路**]。 在右窗格的 [ **VNet 整合**] 底下設定。

   ![新增虛擬網路整合](./media/secure-web-app/app-vnet-menu.png)

    *App Service 的新虛擬網路整合*
1. 在下一個頁面上，選取 [**新增 VNET （預覽）** ]。

1. 在下一個功能表中，選取部署中建立的虛擬網路，其開頭為 `aad-vnet`。 您可以建立新的子網，或選取現有的子網。
   在此情況下，請建立新的子網。 將 [**位址範圍**] 設定為 [ **10.0.3.0/24** ]，並將子網命名為**應用程式子網**。

   ![App Service 虛擬網路設定](./media/secure-web-app/app-vnet-config.png)

    *App Service 的虛擬網路設定*

現在您已啟用虛擬網路整合，您可以將網路安全性群組新增至我們的應用程式。

1. 使用 [搜尋] 方塊，搜尋 [**網路安全性群組**]。 在結果中選取 [**網路安全性群組**]。

    ![搜尋網路安全性群組](./media/secure-web-app/nsg-search-menu.png)

    *搜尋網路安全性群組*

2. 在下一個功能表上，選取 [**新增**]。 輸入 NSG 的**名稱**及其所在的**資源群組**。 此 NSG 將會套用至應用程式閘道的子網。

    ![建立 NSG](./media/secure-web-app/nsg-create-new.png)

    *建立 NSG*

3. 建立 NSG 之後，請選取它。 在其分頁的 [**設定**] 底下，選取 [**輸入安全性規則**]。 設定這些設定，以允許透過埠443進入應用程式閘道的連線。

   ![設定 NSG](./media/secure-web-app/nsg-gateway-config.png)

   *設定 NSG*

4. 在 [閘道 NSG] 的輸出規則中，藉由建立以服務標籤為目標的規則，新增允許 App Service 實例輸出連線的規則 `AppService`

   ![新增 NSG 的輸出規則](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *新增 NSG 的輸出規則*

    新增另一個輸出規則，以允許閘道將輸出規則傳送到虛擬網路。

   ![新增另一個輸出規則](./media/secure-web-app/nsg-outbound-vnet.png)

    *新增另一個輸出規則*

5. 在 NSG 的 [子網] 分頁上，選取 [**關聯**]，選取部署中所建立的虛擬網路，然後選取名為**gw-subnet**的閘道子網。 NSG 會套用至子網。

6. 建立另一個 NSG，如同在先前的步驟中，這次適用于 App Service 實例。 為它命名。 新增埠443的輸入規則，如同您針對應用程式閘道 NSG 所做的一樣。

   如果您將 App Service 實例部署在 App Service 環境實例上（這不是此應用程式的情況），您可以在 App Service NSG 的輸入安全性群組上開啟埠454-455，以新增輸入規則以允許 Azure 服務健康狀態探查。 設定如下：

   ![新增 Azure 服務健康狀態探查的規則](./media/secure-web-app/nsg-create-healthprobes.png)

    *新增 Azure 服務健康狀態探查的規則（僅 App Service 環境）*

若要限制受攻擊面，請修改 App Service 網路設定，只允許應用程式閘道存取應用程式。
若要套用設定，請移至 App Service 網路 索引標籤，選取  **IP 限制** 索引標籤，然後建立允許僅允許應用程式閘道 IP 直接存取服務的允許規則。 您可以從其 [總覽] 頁面取得閘道的 IP 位址。 在 [ **Ip 位址 CIDR** ] 索引標籤上，以下列格式輸入 ip 位址： `<GATEWAY_IP_ADDRESS>/32`。

![僅允許閘道](./media/secure-web-app/app-allow-gw-only.png)

*僅允許閘道 IP 存取 App Service*

### <a name="azure-domain-name-system"></a>Azure 網域名稱系統 
Azure 網域名稱系統（或稱 Azure 網域名稱系統）負責將網站或服務名稱轉譯（或解析）為其 IP 位址。 Azure 網域名稱系統（ https://docs.microsoft.com/azure/dns/dns-overview) 是網域名稱系統網域的主機服務，可使用 Azure 基礎結構來提供名稱解析。 藉由在 Azure 中裝載網域，使用者可以使用與其他 Azure 服務相同的認證、Api、工具和計費方式來管理網域名稱系統記錄。 Azure 網域名稱系統也支援私人網域名稱系統網域。

### <a name="azure-disk-encryption"></a>Azure 磁碟加密
Azure 磁碟加密利用 Windows 的 BitLocker 功能來提供資料磁片的磁片區加密。 此解決方案與 Azure Key Vault 整合，以協助控制和管理磁碟加密金鑰。

### <a name="identity-management"></a>身分識別管理
下列技術提供的功能可管理 Azure 環境中持卡人資料的存取權
- Azure Active Directory 是 Microsoft 的多租使用者雲端式目錄和身分識別管理服務。 此解決方案的所有使用者都是在 Azure Active Directory 中建立，包括存取 Azure WebApp 的使用者。
- Azure 角色型存取控制可讓系統管理員定義微調存取權限，只授與使用者執行其工作所需的存取權數量。 系統管理員可以只允許存取持卡人資料的特定動作，而不是授與所有使用者不受限制的 Azure 資源使用權限。 只有訂用帳戶管理員可擁有訂用帳戶的存取權。
- Azure Active Directory Privileged Identity Management 可讓客戶將可存取特定資訊（例如持卡人資料）的使用者人數降至最低。 系統管理員可以使用 Azure Active Directory Privileged Identity Management 來探索、限制和監視特殊權限的識別和其對資源的存取。 如有需要，這項功能也可用來強制執行隨選 Just-In-Time 系統管理存取權。
- Azure Active Directory Identity Protection 會偵測影響組織身分識別的潛在弱點、設定對偵測到的組織身分識別相關可疑動作的自動回應，以及調查可疑的要採取適當動作來解決問題的事件。
### <a name="secrets-management"></a>祕密管理
解決方案會使用 Azure Key Vault 來管理金鑰和秘密。 Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 下列 Azure Key Vault 功能可協助客戶保護及存取這類資料
   - 進階存取原則是視需要設定的。
   - Key Vault 存取原則是使用金鑰和祕密的最低必要權限所定義的。
   - Key Vault 中的所有金鑰和祕密都有到期日。
   - Key Vault 中的所有金鑰都會受到特製化硬體安全模組的保護。 金鑰類型是硬體安全性模組（HSM）保護的2048位 RSA 金鑰。
   - 使用 Key Vault，您可以加密金鑰和密碼（例如驗證金鑰、儲存體帳戶金鑰、資料加密金鑰）。PFX 檔案和密碼），方法是使用受硬體安全模組（Hsm）保護的金鑰。 
   - 使用以角色為基礎的存取控制（RBAC），將許可權指派給特定範圍的使用者、群組和應用程式。     
   - 使用 Key Vault 以自動更新管理 TLS 憑證。 
   - 金鑰保存庫的診斷記錄的保留期至少 365 天。
   - 允許的金鑰密碼編譯作業僅限於需要的密碼編譯項目。
### <a name="azure-security-center"></a>Azure 資訊安全中心
有了 Azure 資訊安全中心，客戶可以在工作負載之間集中套用及管理安全性原則、限制暴露于威脅的程度，以及偵測和回應攻擊。 附加 
   - Azure 資訊安全中心會存取 Azure 服務的現有設定，以提供設定和服務建議，協助改善安全性狀態並保護資料。
   - Azure 資訊安全中心會使用不同的偵測功能，向客戶警示以其環境為目標的潛在攻擊。 這些警示包含觸發警示的項目、鎖定為目標的資源，以及攻擊來源等重要資訊。 Azure 資訊安全中心具有一組預先定義的安全性警示，會在威脅或可疑活動發生時觸發。 Azure 資訊安全中心中的自訂警示規則可讓客戶根據已從其環境收集的資料，定義新的安全性警示。
   - Azure 資訊安全中心提供依優先順序排列的安全性警示和事件，讓客戶更容易探索及解決潛在的安全性問題。 針對每個偵測到的威脅會產生威脅情報報告，以協助事件回應小組調查威脅並進行補救。
### <a name="azure-application-gateway"></a>Azure 應用程式閘道 
   此架構使用已設定 Web 應用程式防火牆和已啟用 OWASP 規則集的 Azure 應用程式閘道，可減少安全性弱點帶來的風險。 其他功能包括
   - 端對端-SSL。
   - 停用 TLS v1.0 和 v1.1。
   - 啟用 Tlsv1.1 1.2。
   - Web 應用程式防火牆（防止模式）。
   - 具有 OWASP 3.0 規則集的防止模式。
   - 啟用診斷記錄。
   - 自訂健康情況探查。
   - Azure 資訊安全中心和 Azure Advisor 會提供額外的保護和通知。 Azure 資訊安全中心也會提供評價系統。
### <a name="logging-and-auditing"></a>記錄與稽核
Azure 服務會廣泛記錄系統、使用者活動及系統健康情況：
   - 活動記錄：[活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)可讓您深入瞭解在訂用帳戶中的資源上執行的作業。 活動記錄可協助判斷作業的啟動者、發生時間和狀態。
   - 診斷記錄：[診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包含每個資源發出的所有記錄。 這些記錄包含 Windows 事件系統記錄、Azure 儲存體記錄、Key Vault audit 記錄檔，以及應用程式閘道存取和防火牆記錄。 所有診斷記錄都會寫入到集中且加密的 Azure 儲存體帳戶進行封存。 保留期是由使用者自訂，視組織特定的保留期需求，最長可達 730 天。
### <a name="azure-monitor-logs"></a>Azure 監視器記錄
   這些記錄會合並在[Azure 監視器記錄](https://azure.microsoft.com/services/log-analytics/)中，以供處理、儲存及儀表板報告之用。 所收集的資料會針對 Log Analytics 工作區內的每種資料類型組織成個別資料表，以便一起分析所有的資料 (不論其原始來源為何)。 此外，Azure 資訊安全中心與 Azure 監視器記錄整合，可讓客戶使用 Kusto 查詢來存取其安全性事件資料，並將其與來自其他服務的資料合併。

   此架構中包含下列 Azure[監視解決方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)

   - [Active Directory 評定](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：「Active Directory 健康情況檢查」解決方案會定期評估伺服器環境的風險和健康情況，並針對部署的伺服器基礎結構，提供依優先順序排列的建議清單。
   - [代理程式健全狀況](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理程式健全狀況解決方案會報告部署的代理程式數目和其地理分佈，以及沒有回應的代理程式數目和正在提交作業資料的代理程式數量。
   - [活動記錄分析](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：「活動記錄分析」解決方案可協助您分析客戶所有 Azure 訂用帳戶的 Azure 活動記錄。
### <a name="azure-monitor"></a>Azure Monitor
   [Azure 監視器](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)藉由讓組織能夠審核、建立警示及封存資料，包括追蹤其 Azure 資源中的 API 呼叫，協助使用者追蹤效能、維護安全性和識別趨勢。
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)是可延伸的應用程式效能管理服務，適用于多個平臺上的 網頁程式開發人員。 Application Insights 會偵測效能異常，客戶可以用它來監視即時 Web 應用程式。 其中包括強大的分析工具可協助客戶診斷問題，並了解使用者實際上如何運用應用程式。 它是設計來協助客戶持續改善效能和可用性。

### <a name="azure-key-vault"></a>Azure Key Vault
   為用來儲存金鑰的組織建立保存庫，並維護如下所述作業工作的責任

   - 儲存在 Key Vault 中的資料包含   
   - Application Insights 金鑰
   - 資料儲存體存取金鑰
   - 連接字串
   - 資料表名稱
   - 使用者認證
   - 進階存取原則是視需要設定的
   - Key Vault 存取原則會以金鑰和密碼的最低必要許可權定義
   - 金鑰保存庫中的所有金鑰和密碼都有到期日
   - Key Vault 中的所有金鑰都受到硬體安全模組（HSM）的保護（金鑰類型 = 硬體安全性模組（HSM）保護）       
     2048-位 RSA 金鑰]
   - 使用以角色為基礎的存取控制（RBAC）授與所有使用者/身分識別所需的最低許可權
   - 應用程式不會共用金鑰保存庫，除非它們彼此信任且必須在執行階段存取相同金鑰
   - 金鑰保存庫的診斷記錄的保留期至少 365 天。
   - 允許的金鑰密碼編譯作業僅限於需要的密碼編譯項目

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
   安全的 VPN 通道或[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)必須透過安全地建立與部署為此 PaaS web 應用程式參考架構一部分之資源的連線來設定。 透過適當地設定 VPN 或 ExpressRoute，客戶可以在傳輸過程中新增資料保護層。

   藉由實作與 Azure 的安全 VPN 通道，即可建立內部部署網路與 Azure 虛擬網路之間的虛擬私人連線。 此連線透過網際網路進行，可讓客戶在客戶網路與 Azure 之間的加密連結內安全地「通道」資訊。 站對站 VPN 是安全成熟的技術，各種規模的企業已部署數十年。 此選項會使用 IPsec 通道模式作為加密機制。

   由於 VPN 通道內的流量會透過站對站 VPN 周遊網際網路，因此 Microsoft 提供另一個更安全的連線選項。 Azure ExpressRoute 是 Azure 與內部部署位置或 Exchange 主機服務提供者之間專用的 WAN 連結。 ExpressRoute 連線不會經過網際網路，相較於一般網際網路連線，這些連線提供了更可靠、更快速、更低延遲、更安全的連線。 此外，因為這是客戶電信提供者的直接連線，所以資料不會透過網際網路傳輸，因此不會公開給網際網路。

   如需實作將內部部署網路擴充至 Azure 之安全混合式網路的最佳做法，請參閱[這裡](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

#### <a name="implement-azure-active-directory-oidc"></a>執行 Azure Active Directory OIDC

1. 若要複製原始程式碼存放庫，請使用此 Git 命令

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>更新重新導向 Url
1.  流覽回到 Azure 入口網站。 在左側導覽窗格中，選取 [Azure Active Directory] 服務，然後選取 [應用程式註冊]。
2.  在結果畫面中，選取 [WebApp-OpenIDConnect-DotNet-code-v2] 應用程式。
3.  在 [重新導向 Uri] 區段的 [驗證] 索引標籤中，選取下拉式方塊中的 [Web]，然後新增下列重新導向 Uri。
    [https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net](https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net ) https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc [Advanced settings] 區段中的 o 設定 [登出 URL] https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  在 [商標] 索引標籤中，將首頁 URL 更新為 app service 的位址，例如 https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net 。
        o 儲存設定。
5.  如果您的應用程式呼叫 web api，請務必在專案 appsettings 上套用必要的變更，因此它會呼叫已發佈的 API URL，而不是 localhost。
發行範例
    1.  從 App Service 的 [總覽] 索引標籤中，按一下 [取得發行設定檔] 連結並加以儲存，以下載發行設定檔。 您也可以使用其他部署機制，例如從原始檔控制。
    2.  切換至 Visual Studio 並移至 [WebApp-OpenIDConnect-DotNet-code-v2] 專案。 以滑鼠右鍵按一下方案總管中的專案，然後選取 [發佈]。 按一下底部列上的 [匯入設定檔]，然後匯入您稍早下載的發行設定檔。
    3.  按一下 [設定]，然後在 [連線] 索引標籤中更新 [目的地 URL]，讓它在首頁 url 中是 HTTPs，例如 https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net 。 按 [下一步]。
    4.  在 [設定] 索引標籤上，請確定未選取 [啟用組織驗證]。 按一下 [儲存]。 按一下主畫面上的 [發佈]。
    5.  Visual Studio 會發佈專案，並自動開啟瀏覽器並導向至專案的 URL。 如果您看到專案的預設網頁，表示發佈成功。
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>執行 Azure Active Directory 的多重要素驗證
   系統管理員必須確保入口網站中的訂用帳戶帳戶受到保護。 此訂用帳戶容易遭受攻擊，因為它會管理您所建立的資源。 若要保護訂用帳戶，請在訂用帳戶的 [ **Azure Active Directory** ] 索引標籤上啟用多重要素驗證。

   Azure AD 會根據套用至符合特定準則的使用者或使用者群組的原則進行操作。
Azure 會建立預設原則，指定系統管理員需要雙因素驗證來登入入口網站。
啟用此原則之後，系統可能會提示您登出，然後重新登入 Azure 入口網站。

啟用管理登入的 MFA

   1. 移至 Azure 入口網站中的 [ **Azure Active Directory** ] 索引標籤
   2. 在 [安全性] 類別底下，選取 [條件式存取]。 您會看到此畫面

       ![條件式存取-原則](./media/secure-aad-app/ad-mfa-conditional-add.png)

如果您無法建立新的原則

   1. 移至 [ **MFA** ] 索引標籤。
   2. 選取 Azure AD Premium**免費試用版**連結以訂閱免費試用版。

   ![Azure AD Premium 免費試用](./media/secure-aad-app/ad-trial-premium.png)

返回 [條件式存取] 畫面。

   1. 選取 [新增原則] 索引標籤。
   2. 輸入原則名稱。
   3. 選取您想要啟用 MFA 的使用者或群組。
   4. 在 [**存取控制**] 底下，選取 [**授**與] 索引標籤，然後選取 [**需要多重要素驗證**] \ （以及其他設定，如果需要的話）。

   ![需要 MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   您可以選取畫面頂端的核取方塊來啟用原則，或在 [**條件式存取**] 索引標籤上執行此動作。當原則啟用時，使用者需要 MFA 才能登入入口網站。

   所有 Azure 系統管理員都需要使用 MFA 的基準原則。 您可以立即在入口網站中啟用它。 啟用此原則可能會使目前的會話無效，並強制您重新登入。

   如果未啟用基準原則
   1.   選取 [**需要系統管理員的 MFA**]。
   2.   選取 [**立即使用原則**]。

   ![選取 [立即使用原則]](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>使用 Azure Sentinel 來監視應用程式和資源

   隨著應用程式成長，就難以匯總從資源接收的所有安全性信號和計量，並使其以動作導向的方式發揮作用。

   Azure Sentinel 的設計是用來收集資料、偵測可能的威脅類型，以及提供安全性事件的可見度。
雖然它會等待手動操作，但 Azure Sentinel 可以依賴預先撰寫的操作手冊來啟動警示和事件管理流程。

   範例應用程式是由 Azure Sentinel 可以監視的數個資源所組成。
若要設定 Azure Sentinel，您必須先建立 Log Analytics 工作區，以儲存從各種資源收集而來的所有資料。

若要建立此工作區

   1. 在 Azure 入口網站的 搜尋 方塊中，搜尋**Log Analytics**。 選取 [Log Analytics 工作區]。

   ![搜尋 Log Analytics 工作區](./media/secure-aad-app/sentinel-log-analytics.png)

   *搜尋 Log Analytics 工作區*

   2. 在下一個頁面上，選取 [**新增**]，然後提供工作區的名稱、資源群組和位置。
   ![建立 Log Analytics 工作區](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *建立 Log Analytics 工作區*

   3. 使用 [搜尋] 方塊來搜尋**Azure Sentinel**。

   ![搜尋 Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *搜尋 Azure Sentinel*

   4. 選取 [**新增**]，然後選取您稍早建立的 Log Analytics 工作區。

   ![新增 Log Analytics 工作區](./media/secure-aad-app/sentinel-workspace-add.png)

   *新增 Log Analytics 工作區*

   5. 在 [ **Azure Sentinel-資料連線器**] 頁面的 [設定 **] 下，** 選取 [**資料連線器**]。 您會看到一組 Azure 服務，您可以連結到 Log Analytics 儲存體實例，以便在 Azure Sentinel 中進行分析。

   ![Log Analytics 資料連線器](./media/secure-aad-app/sentinel-connectors.png)

      *將資料連線器新增至 Azure Sentinel*

   例如，若要連接應用程式閘道，請執行下列步驟：

   1. 開啟 [Azure 應用程式閘道實例] 分頁。
   2. 在 [監視] 下方，選取 [診斷設定]。
   3. 選取 [**新增診斷設定**]。

   ![新增應用程式閘道診斷](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *新增應用程式閘道診斷*

   4. 在 [**診斷設定**] 頁面上，選取您所建立的 Log Analytics 工作區，然後選取您想要收集並傳送至 Azure Sentinel 的所有計量。 選取 [儲存]。

   ![Azure Sentinel 連接器設定](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>成本考量
   如果您還沒有 Azure 帳戶，可以建立一個免費帳戶。 前往[免費帳戶頁面](https://azure.microsoft.com/free/)以開始使用、瞭解免費 Azure 帳戶可執行檔工作，以及學習哪些產品免費12個月。

   若要使用安全性功能來部署範例應用程式中的資源，您必須支付一些 premium 功能。 當應用程式調整規模，且 Azure 所提供的免費層和試用版需要升級以符合應用程式需求時，您的成本可能會增加。 使用 Azure[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估您的成本。

## <a name="next-steps"></a>後續步驟
   下列文章可協助您設計、開發和部署安全的應用程式。

- [設計](secure-design.md)
- [開發](secure-develop.md)
- [部署](secure-deploy.md)
