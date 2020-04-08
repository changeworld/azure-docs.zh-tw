---
title: 開發安全的 Azure AD Web 應用程式 |微軟文件
description: 此簡單示例應用實現了安全最佳實踐,在 Azure 上開發時可改進應用程式和組織的安全狀態。
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
ms.openlocfilehash: 599c4a31840b47294b43c4c4d1f0200b17f04540
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810543"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>為 Azure AD 應用開發安全應用
## <a name="overview"></a>概觀

此示例是一個簡單的 Azure 活動目錄,帶有 Web 應用,用於連結到用於在 Azure 上開發應用的安全資源。 該應用程式實現了安全最佳實踐,這些最佳實踐可説明改進應用程式和組織在 Azure 上開發應用時的安全狀況。

部署腳本設置基礎結構。 運行部署腳本後,需要在 Azure 門戶中執行一些手動配置,以將元件和服務連結在一起。 此示例面向 Azure 上經驗豐富的開發人員,他們在零售行業中工作,並希望使用安全的 Azure 基礎結構構建安全的 Azure 活動目錄。 


在開發並部署此應用程式時,您將學習如何 
- 創建 Azure 金鑰保管庫實例,儲存並從中檢索機密。
- 部署 Azure Web 應用,該應用專用於與前端防火牆訪問隔離。 
- 使用使用 OWASP 前 10 個規則集的防火牆創建和配置 Azure 應用程式閘道實例。 
- 使用 Azure 服務對傳輸中和靜態的數據進行加密。 
- 設置 Azure 策略和安全中心以評估相容性。 

開發和部署此應用程式後,您將設置以下範例 Web 應用以及描述的配置和安全措施。

## <a name="architecture"></a>架構
該應用程式是一個典型的n層應用程式,具有三層。 此處顯示了整合了監視和金鑰管理元件的前端、後端和資料庫層:

![應用程式架構](./media/secure-aad-app/architecture.png)

此解決方案會使用下列 Azure 服務。 部署體系結構的詳細資訊位於「部署體系結構」 部分。 

結構結構由這些元件組成

- [Azure 應用程式閘道](../../application-gateway/index.yml)。 為我們的應用程式體系結構提供閘道和防火牆。
- [應用程式見解](../../azure-monitor/app/app-insights-overview.md)。 在多個平臺上提供可擴充的應用程式性能管理 (APM) 服務。
- [Azure 金鑰保存函式庫](../../key-vault/index.yml)。 存儲和加密應用的機密,並管理圍繞其創建訪問策略。
- [Azure 的活動目錄](../../active-directory/fundamentals/active-directory-whatis.md)。提供基於雲的標識和存取管理服務、登錄和存取資源。
- [Azure 網域名稱系統](../../dns/dns-overview.md)。 提供託管域的服務。
- [Azure 負載均衡器](../../load-balancer/load-balancer-overview.md)。 提供擴展應用程式並為服務創建高可用性。
- [Azure Web 應用程式](../../app-service/overview.md)。  提供基於 HTTP 的服務來託管 Web 應用程式。
- [Azure 安全中心](../../security-center/index.yml)。 跨雲中的混合工作負載提供高級威脅保護。
- [Azure 政策](../../governance/policy/overview.md). 提供評估資源以不符合分配的策略。

## <a name="threat-model"></a>威脅模型
威脅建模是識別業務和應用程式的潛在安全威脅,然後確保制定適當的緩解計劃的過程。

此示例使用[Microsoft 威脅建模工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)為安全示例應用實現威脅建模。 通過繪製元件和數據流圖,可以在開發過程的早期識別問題和威脅。 時間和金錢將節省以後使用這個。

下面是範例應用的威脅模型

![威脅模型](./media/secure-aad-app/threat-model.png)

以下螢幕截圖中顯示了威脅建模工具生成的一些示例威脅和潛在漏洞。 威脅模型概述了暴露的攻擊面,並提示開發人員考慮如何緩解問題。

![威脅模型輸出](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Prerequisites
要啟動與執行應用程式,您需要安裝這些工具:

- 用於修改和查看應用程式代碼的代碼編輯器。[可視化工作室代碼](https://code.visualstudio.com/)是一個開源選項。
- [開發電腦上的 Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,)
- [Git](https://git-scm.com/)在你的系統上。 Git 用於在本地克隆原始程式碼。
- [jq,](https://stedolan.github.io/jq/)一種以使用者友好方式查詢JSON的UNIX工具。

您需要 Azure 訂閱來部署範例應用的資源。 如果沒有 Azure 訂閱,則可以[創建一個免費帳戶](https://azure.microsoft.com/free/)來測試示例應用。

安裝這些工具後,即可在 Azure 上部署應用。

### <a name="implementation-guidance"></a>實作指引
部署腳本是一個腳本,可以分解為四個階段。 每個階段部署和配置[體系結構關係圖](#architecture)中的 Azure 資源。

四個階段是

- 部署 Azure 金鑰保管庫。
- 部署 Azure Web 應用。
- 使用 Web 應用程式防火牆部署應用程式閘道。
- 使用已部署的應用配置 Azure AD。

每個階段都使用以前部署的資源中的配置在前一個階段構建。

要完成實施步驟,請確保已安裝「[先決條件](#prerequisites)」下列出的工具。

#### <a name="deploy-azure-key-vault"></a>部署 Azure 金鑰保存庫
在本節中,創建和部署用於儲存機密和證書的 Azure 密鑰保管庫實例。

完成部署後,在 Azure 上部署了 Azure 密鑰保管庫實例。

使用 Powershell 部署 Azure 金鑰保管庫
 
1. 聲明 Azure 密鑰保管庫的變數。
2. 註冊 Azure 金鑰保管庫提供程式。
3. 為實例創建資源組。
4. 在步驟 3 中創建的資源組中創建 Azure 密鑰保管庫實例。

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>以下 Azure AD 使用者會對金鑰保管庫具有管理員權限
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>註冊 Az 提供者
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>建立 Azure 金鑰保存庫實例
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>將管理員政策加入金鑰保存庫
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>建立存取原則以允許使用者取得與列出加密金鑰、證書和機密(如果您知道使用者主體名稱):
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

最佳做法是在使用密鑰保管庫存取資源的應用中對 Azure 資源使用託管標識。 當金鑰保管庫的訪問密鑰未存儲在代碼或配置中時,您的安全狀態會提高。

容器中包含根證書。 取得憑證的步驟包括

1. 從[憑證頒發機構](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)下載證書檔。
2. 解碼憑證檔:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
此腳本為應用服務實例創建分配的身份,該標識可與 MSI 一起使用,與 Azure 密鑰保管庫進行交互,而不會在代碼或配置中使用硬編碼機密。

轉到門戶中的 Azure 金鑰保管庫實例,以授權存取策略選項卡上分配的標識。選擇 **"添加新存取策略**"。 在**Select 主體**下,搜索與創建的應用服務實例的名稱類似的應用程式名稱。
附加到應用程式的服務主體應可見。 選擇它並保存訪問策略頁,如下圖所示。

由於應用程式只需要檢索密鑰,因此在機密選項中選擇 **「獲取**」許可權,允許訪問,同時減少授予的許可權。

![Key Vault 存取原則](./media/secure-aad-app/kv-access-policy.png)

*建立金鑰保存者存取原則*

保存存取策略,然後在 **「存取策略」** 選項卡上儲存新更改以更新策略。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>在啟用 Web 應用程式防火牆後部署應用程式閘道
在 Web 應用中,不建議您在網路上直接向外部世界公開服務。
負載平衡和防火牆規則為傳入流量提供了更多的安全性和控制,並説明您對其進行管理。

部署應用程式閘道實式

1. 創建資源組以容納應用程式閘道。
2. 預配虛擬網路以附加到閘道。
3. 為虛擬網路中的閘道創建子網。
4. 預配公共 IP 位址。
5. 預配應用程式閘道。
6. 在閘道上啟用 Web 應用程式防火牆。

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

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>部署 Azure Web 應用
Azure 應用服務使您能夠使用 Python、Ruby、C# 和 Java 等語言建構和託管 Web 應用。 Azure 還支援自定義容器,它允許在 Azure 應用服務平臺上運行幾乎所有程式設計語言。

#### <a name="create-an-app-service-plan-in-free-tier"></a>在免費套餐中建立應用服務計劃
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>建立 Web 應用程式
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>在繼續之前,請轉到自訂網域的 Azure 功能變數名稱系統設定https://aka.ms/appservicecustomdnsUI,然後按照以下 說明為主機名「www」設定 CNAME 記錄,並將其指向 Web 應用的預設功能變數名稱

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>將應用服務計劃升級到共用層(自訂網域所需的最低要求)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>新增 Web 應用程式加入自訂網域名稱
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>指引與建議

### <a name="network"></a>網路
完成部署後,您有一個啟用了 Web 應用程式防火牆的應用程式閘道。

閘道實例公開 HTTPS 埠 443。 此配置可確保我們的應用只能透過 HTTPS 在埠 443 上訪問。

阻止未使用的埠和限制攻擊面暴露是一種安全最佳做法。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>將網路安全群組加入到應用程式服務實體

應用服務實例可以與虛擬網路整合。 此整合允許使用管理應用傳入和傳出流量的網路安全組策略配置它們。

1. 要啟用此功能,請在 Azure 應用服務實體邊欄選項卡上,**在「設定」** 下選擇 **「網路**」 。 在右邊窗格中, 在**VNet 整合**下設定 。

   ![新的虛擬網路整合](./media/secure-web-app/app-vnet-menu.png)

    *套用服務的新虛擬網路整合*
1. 在下一頁上,選擇 **「添加 VNET(預覽)」。。**

1. 在下一個功能表上,選擇在以`aad-vnet`開始的部署中創建的虛擬網路。 您可以建立新子網或選擇現有子網。
   在這種情況下,創建一個新的子網。 將**位址範圍**設定為**10.0.3.0/24,** 並命名子**網應用子網**。

   ![套用服務虛擬網路設定](./media/secure-web-app/app-vnet-config.png)

    *套用服務的虛擬網路設定*

現在,您已經啟用了虛擬網路集成,您可以將網路安全組添加到我們的應用。

1. 使用搜尋框,搜尋**網路安全群組**。 在結果中選擇**網路安全群組**。

    ![搜尋網路安全群組](./media/secure-web-app/nsg-search-menu.png)

    *搜尋網路安全群組*

2. 在下一個功能表上,選擇 **「添加**」。。 輸入此的 NSG**和資源群組****的名稱**。 此 NSG 將應用於應用程式閘道的子網。

    ![建立 NSG](./media/secure-web-app/nsg-create-new.png)

    *建立 NSG*

3. 創建 NSG 後,選擇它。 在其邊欄選項卡中,在 **「設置」** 下,選擇 **「入站安全規則**」。 配置這些設置以允許通過埠 443 進入應用程式閘道的連接。

   ![設定 NSG](./media/secure-web-app/nsg-gateway-config.png)

   *設定 NSG*

4. 在閘道 NSG 的出站規則中,透過建立以服務標記為目標的規則,加入允許出站連接到應用服務實例的規則`AppService`

   ![新增 NSG 的出站規則](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *新增 NSG 的出站規則*

    添加另一個出站規則,允許閘道將出站規則發送到虛擬網路。

   ![新增其他出站規則](./media/secure-web-app/nsg-outbound-vnet.png)

    *新增其他出站規則*

5. 在 NSG 的子網路欄選項卡上,選擇 **「關聯**」,選擇在部署中建立的虛擬網路,然後選擇名為**gw-子網路的**閘道 。 NSG 應用於子網。

6. 創建另一個 NSG,如前面的步驟中,此時為應用服務實例。 給它一個名字。 添加埠 443 的入站規則,就像對應用程式網關 NSG 所做的那樣。

   如果在應用服務環境實例上部署了應用服務實例(此應用不是這種情況,則可以通過打開應用服務 NSG 的入站安全組上的埠 454-455 來添加入站規則以允許 Azure 服務運行狀況探測。 設定:

   ![新增 Azure 服務執行狀況偵測的規則](./media/secure-web-app/nsg-create-healthprobes.png)

    *新增 Azure 服務執行狀況偵測規則(僅限應用服務環境)*

要限制攻擊面,請修改應用服務網路設置,以僅允許應用程式閘道存取應用程式。
要應用設定,請轉到應用服務網路選項卡,選擇 **「IP 限制」** 選項卡,並創建只允許應用程式閘道的 IP 直接存取服務的允許規則。 可以從閘道的概述頁檢索閘道的 IP 位址。 IP**位址 CIDR**選項卡上,輸入此`<GATEWAY_IP_ADDRESS>/32`格式的 IP 位址: 。

![只允許閘道](./media/secure-web-app/app-allow-gw-only.png)

*只允許閘道 IP 存取應用服務*

### <a name="azure-domain-name-system"></a>Azure 網域名稱系統 
Azure 網域名稱系統(或 Azure 網域名稱系統)負責將網站或服務名稱翻譯(或解析)到其 IP 位址。 Azure 網域名稱https://docs.microsoft.com/azure/dns/dns-overview)系統( 是使用 Azure 基礎結構提供名稱解析的網域名稱系統域的託管服務)。 通過在 Azure 中託管域,用戶可以使用與其他 Azure 服務相同的認證、API、工具和計費來管理網域名稱系統記錄。 Azure 功能變數名稱系統還支援專用域名稱系統域。

### <a name="azure-disk-encryption"></a>Azure 磁碟加密
Azure 磁碟加密利用 Windows 的 BitLocker 功能來提供資料磁碟的磁碟區加密。 此解決方案與 Azure Key Vault 整合，以協助控制和管理磁碟加密金鑰。

### <a name="identity-management"></a>身分識別管理
以下技術提供了在 Azure 環境中管理對持卡人資料的存取的功能
- Azure 活動目錄是Microsoft的多租戶基於雲的目錄和標識管理服務。 此解決方案的所有使用者都在 Azure 活動目錄中創建,包括造訪 Azure WebApp 的使用者。
- Azure 角色型存取控制可讓系統管理員定義微調存取權限，只對使用者授與其執行工作所需的存取權數量。 系統管理員可以只允許存取持卡人資料的特定動作，而不是授與所有使用者不受限制的 Azure 資源使用權限。 只有訂用帳戶管理員可擁有訂用帳戶的存取權。
- Azure Active Directory Privileged Identity Management 可讓客戶將可存取特定資訊 (例如持卡人資料) 的使用者人數降至最低。 系統管理員可以使用 Azure Active Directory Privileged Identity Management 來探索、限制和監視特殊權限的識別和其對資源的存取。 如有需要，這項功能也可用來強制執行隨選 Just-In-Time 系統管理存取權。
- Azure 活動目錄標識保護可檢測影響組織身份的潛在漏洞,配置自動回應,以檢測與組織標識相關的可疑操作,並調查可疑事件以採取適當的措施解決它們。
### <a name="secrets-management"></a>祕密管理
此解決方案會使用 Azure Key Vault 來管理金鑰和秘密。 Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 以下 Azure 金鑰保管庫功能可幫助客戶保護和存取此類資料
   - 進階存取原則是視需要設定的。
   - Key Vault 存取原則是使用金鑰和祕密的最低必要權限所定義的。
   - Key Vault 中的所有金鑰和祕密都有到期日。
   - Key Vault 中的所有金鑰都會受到特製化硬體安全模組的保護。 密鑰類型是硬體安全模組 (HSM) 保護的 2048 位 RSA 密鑰。
   - 使用金鑰保管庫,您可以加密金鑰和機密(如身份驗證金鑰、儲存帳戶金鑰、資料加密金鑰、通過使用受硬體安全模組 (HSM) 保護的密鑰,PFX 檔和密碼。 
   - 使用基於角色的存取控制 (RBAC) 將許可權分配給特定範圍內的使用者、組和應用程式。     
   - 使用金鑰保管庫通過自動續訂管理 TLS 證書。 
   - 金鑰保存庫的診斷記錄的保留期至少 365 天。
   - 允許的金鑰密碼編譯作業僅限於需要的密碼編譯項目。
### <a name="azure-security-center"></a>Azure 資訊安全中心
客戶可以使用 Azure 資訊安全中心，在工作負載之間集中套用及管理安全性原則、限制暴露於威脅的程度，以及偵測和回應攻擊。 此外 
   - Azure 安全中心訪問 Azure 服務的現有配置,以提供配置和服務建議,以説明改進安全狀態和保護數據。
   - Azure 資訊安全中心會使用不同的偵測功能，向客戶警示以其環境為目標的潛在攻擊。 這些警示包含觸發警示的項目、鎖定為目標的資源，以及攻擊來源等重要資訊。 Azure 資訊安全中心有一組預先定義的安全性警示，一旦發生威脅或可疑活動時便會觸發這些警示。 Azure 資訊安全中心內的自訂警示規則可讓客戶根據從其環境所收集到的資料，定義新的安全性警示。
   - Azure 資訊安全中心提供依優先順序排列的安全性警示和事件，讓客戶更容易探索及解決潛在的安全性問題。 針對每個偵測到的威脅會產生威脅情報報告，以協助事件回應小組調查威脅並進行補救。
### <a name="azure-application-gateway"></a>Azure 應用程式閘道 
   此架構使用已設定 Web 應用程式防火牆和已啟用 OWASP 規則集的 Azure 應用程式閘道，可減少安全性弱點帶來的風險。 其他功能包括
   - 端到端 TLS。
   - 停用 TLS v1.0 和 v1.1。
   - 啟用 TLSv1.2。
   - Web 應用程式防火牆(預防模式)。
   - 具有 OWASP 3.0 規則集的預防模式。
   - 啟用診斷日誌記錄。
   - 自定義運行狀況探測。
   - Azure 安全中心和 Azure 顧問提供其他保護和通知。 Azure 資訊安全中心也會提供評價系統。
### <a name="logging-and-auditing"></a>記錄與稽核
Azure 服務會廣泛記錄系統、使用者活動及系統健康情況：
   - 活動日誌:[活動日誌](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供對訂閱中資源執行的操作的見解。 活動記錄可協助判斷作業的啟動者、發生時間和狀態。
   - 診斷日誌:[診斷日誌](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每個資源發出的所有日誌。 這些日誌包括 Windows 事件系統日誌、Azure 儲存日誌、密鑰保管庫審核日誌以及應用程式網關訪問和防火牆日誌。 所有診斷記錄都會寫入到集中且加密的 Azure 儲存體帳戶進行封存。 保留期是由使用者自訂，視組織特定的保留期需求，最長可達 730 天。
### <a name="azure-monitor-logs"></a>Azure 監視器記錄
   這些日誌合併在[Azure 監視器日誌](https://azure.microsoft.com/services/log-analytics/)中,用於處理、存儲和儀錶板報告。 所收集的資料會針對 Log Analytics 工作區內的每種資料類型組織成個別資料表，以便一起分析所有的資料 (不論其原始來源為何)。 此外,Azure 安全中心與 Azure 監視器日誌整合,允許客戶使用 Kusto 查詢訪問其安全事件數據並將其與其他服務的數據合併。

   以下 Azure[監視解決方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)作為此體系結構的一部分包含在內

   - [活動目錄評估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):活動目錄運行狀況檢查解決方案在常規時間間隔內評估伺服器環境的風險和運行狀況,並提供特定於已部署伺服器基礎結構的建議的優先順序清單。
   - [代理運行狀況](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):代理運行狀況解決方案報告部署的代理數量及其地理分佈,以及回應回應的代理數量以及提交操作數據的代理數。
   - [活動記錄分析](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：「活動記錄分析」解決方案可協助您分析客戶所有 Azure 訂用帳戶的 Azure 活動記錄。
### <a name="azure-monitor"></a>Azure 監視器
   [Azure 監視器](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)透過使組織能夠稽核、建立警報和存檔資料(包括追蹤其 Azure 資源中的 API 呼叫)説明使用者追蹤效能、維護安全性和識別趨勢。
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多個平台上的 Web 開發人員所適用的可延伸「應用程式效能管理」服務。 Application Insights 會偵測效能異常，客戶可以用它來監視即時 Web 應用程式。 其中包括強大的分析工具可協助客戶診斷問題，並了解使用者實際上如何運用應用程式。 它是設計來協助客戶持續改善效能和可用性。

### <a name="azure-key-vault"></a>Azure 金鑰保存庫
   為存儲金鑰的組織創建保管庫,並維護操作任務的問責制,如下所示

   - 儲存在金鑰保存庫中的資料   
   - Application Insights 金鑰
   - 資料儲存存取金鑰
   - 連接字串
   - 資料表名稱
   - 使用者認證
   - 進階存取原則是視需要設定的
   - 金鑰保管庫存取策略的定義具有對金鑰和機密的最低要求權限
   - 金鑰保存庫中的所有金鑰和密碼都有到期日
   - 金鑰保管庫中的所有金鑰都受硬體安全模組 (HSM) [金鑰類型 = 硬體安全模組 (HSM) 保護保護       
     2048 位元 RSA 金鑰*
   - 使用基於角色的存取控制 (RBAC) 給所有使用者/識別所需的最低權限
   - 應用程式不會共用金鑰保存庫，除非它們彼此信任且必須在執行階段存取相同金鑰
   - 金鑰保存庫的診斷記錄的保留期至少 365 天。
   - 允許的金鑰密碼編譯作業僅限於需要的密碼編譯項目

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
   需要透過安全地建立與作為此 PaaS Web 應用程式參考體系結構一部分部署的資源的連接來配置安全的 VPN 隧道或[ExpressRoute。](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 透過適當地設定 VPN 或 ExpressRoute，客戶可以在傳輸過程中新增資料保護層。

   藉由實作與 Azure 的安全 VPN 通道，即可建立內部部署網路與 Azure 虛擬網路之間的虛擬私人連線。 此連接透過 Internet 進行,並允許客戶安全地在客戶網路和 Azure 之間的加密鏈路內「隧道」資訊。 站對站 VPN 是安全成熟的技術，各種規模的企業已部署數十年。 此選項使用 IPsec 通道模式作為加密機制。

   由於 VPN 通道內的流量會透過站對站 VPN 周遊網際網路，因此 Microsoft 提供另一個更安全的連線選項。 Azure ExpressRoute 是 Azure 與內部部署位置或 Exchange 主機服務提供者之間專用的 WAN 連結。 ExpressRoute 連線不會經過網際網路，相較於一般網際網路連線，這些連線提供了更可靠、更快速、更低延遲、更安全的連線。 此外，因為這是客戶電信提供者的直接連線，所以資料不會透過網際網路傳輸，因此不會公開給網際網路。

   如需實作將內部部署網路擴充至 Azure 之安全混合式網路的最佳做法，請參閱[這裡](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

#### <a name="implement-azure-active-directory-oidc"></a>完整使用 Azure 的目錄 OIDC

1. 要複製原始碼儲存函式庫,請使用此 Git 命令

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>更新重定到網址
1.  導航回 Azure 門戶。 在左側導航窗格中,選擇 Azure 活動目錄服務,然後選擇應用註冊。
2.  在結果的螢幕中,選擇 WebApp-OpenIDConnect-DotNet 代碼 v2 應用程式。
3.  在「身份驗證」選項卡 o 在「重定向 URI」部分中,在組合框中選擇 Web 並添加以下重定向 URI。
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o 在「進階設定」部分中,將登出網址設定為https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  在「品牌」選項卡 o 將主頁網址更新到應用服務的https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net位址,例如 。
        o 保存配置。
5.  如果應用程式呼叫 Web api,請確保在專案應用設定.json 上應用必要的更改,以便呼叫已發布的 API URL 而不是本地主機。
發佈範例
    1.  從 App Service 的 [概觀] 索引標籤中，按一下 [取得發行設定檔] 連結以下載發行設定檔，並加以儲存。 您也可以使用其他部署機制，例如從原始檔控制。
    2.  切換到可視化工作室,然後轉到 WebApp-OpenIDConnect-DotNet 代碼 v2 專案。 以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取 [發佈]。 按一下底部列上的 [匯入設定檔]，然後匯入您先前下載的發行設定檔。
    3.  點選「設定」 的連線「連線」 選項中更新目標網址,使其成為主頁網址中的https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.neths,例如 。 按 [下一步]。
    4.  在「設定」選項卡上,確保未選擇啟用組織身份驗證。 按一下 [儲存]。 按一下主畫面上的 [發佈]。
    5.  Visual Studio 會發佈專案，並自動開啟瀏覽器並導向至專案的 URL。 如果您看到專案的預設網頁，表示發佈成功。
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>完整可使用 Azure 的目錄的多重身份驗證
   管理員需要確保門戶中的訂閱帳戶受到保護。 訂閱容易受到攻擊,因為它管理您創建的資源。 為了保護訂閱,請在訂閱的**Azure 活動目錄**選項卡上啟用多重身份驗證。

   Azure AD 基於應用於符合特定條件的使用者或使用者組的策略進行操作。
Azure 創建一個預設策略,指定管理員需要雙重身份驗證才能登錄到門戶。
啟用此策略後,系統可能會提示您登出並重新登錄到 Azure 門戶。

為管理員登入 MFA

   1. 跳到 Azure 門戶中的**Azure 的目錄**選項卡
   2. 在安全類別下,選擇條件訪問。 您看到此螢幕

       ![條件存取 - 政策](./media/secure-aad-app/ad-mfa-conditional-add.png)

如果無法建立新原則

   1. 轉到**MFA**選項卡。
   2. 選擇 Azure AD 進階**免費試用版**連結以訂閱免費試用版。

   ![Azure AD 進階免費試用版](./media/secure-aad-app/ad-trial-premium.png)

返回到條件訪問螢幕。

   1. 選擇新的策略選項卡。
   2. 輸入原則名稱。
   3. 選擇要為其啟用 MFA 的使用者或組。
   4. 在 **「存取」控制件**下,選擇「**授予**」選項卡,然後選擇 **「需要多重身份驗證**」(如果需要,則選擇其他設置)。

   ![需要 MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   您可以通過選擇螢幕頂部的複選框來啟用策略,也可以在 **『條件存取**』選項卡上啟用策略。啟用策略後,使用者需要 MFA 才能登錄到門戶。

   有一個基線策略,它要求所有 Azure 管理員使用 MFA。 您可以立即在門戶中啟用它。 啟用此策略可能會使當前會話無效,並迫使您再次登錄。

   如果未開啟基線原則
   1.   為**管理員選擇"需要 MFA」。**
   2.   **選擇立即使用政策**。

   ![立即選擇使用政策](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>使用 Azure 哨兵監視應用和資源

   隨著應用程式的增長,很難聚合從資源接收的所有安全信號和指標,並使它們以面向操作的方式有用。

   Azure Sentinel 旨在收集數據、檢測可能的威脅類型,並提供安全事件的可見性。
在等待手動干預時,Azure Sentinel 可以依賴預先編寫的行動手冊來啟動警報和事件管理過程。

   範例應用由 Azure Sentinel 可以監視的多個資源組成。
要設置 Azure Sentinel,首先需要創建日誌分析工作區,該工作區存儲從各種資源收集的所有數據。

建立此工作區

   1. 在 Azure 門戶中的搜尋框中,搜尋**紀錄分析**。 選取 [Log Analytics 工作區]****。

   ![搜尋紀錄分析工作區](./media/secure-aad-app/sentinel-log-analytics.png)

   *搜尋紀錄分析工作區*

   2. 在下一頁上,選擇 **「添加**」,然後為工作區提供名稱、資源組和位置。
   ![建立 Log Analytics 工作區](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *建立紀錄分析工作區*

   3. 使用搜尋框搜尋 Azure**哨兵**。

   ![搜尋 Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *搜尋 Azure 哨兵*

   4. 選擇 **'添加**",然後選擇之前創建的日誌分析工作區。

   ![新增紀錄分析工作區](./media/secure-aad-app/sentinel-workspace-add.png)

   *新增紀錄分析工作區*

   5. 在**Azure 哨兵 - 資料連接器**頁上,在**配置**下,選擇 **「數據連接器**」。 您將看到一系列 Azure 服務,可以連結到 Azure Sentinel 中的日誌分析儲存實例進行分析。

   ![紀錄分析資料連接器](./media/secure-aad-app/sentinel-connectors.png)

      *加入 Azure 哨兵新增資料連接器*

   例如,要連接應用程式閘道,請執行以下步驟:

   1. 打開 Azure 應用程式閘道實例邊欄選項卡。
   2. 在 [監視]**** 下方，選取 [診斷設定]****。
   3. 選擇 **「添加診斷設置**」。

   ![新增應用程式閘道診斷](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *新增應用程式閘道診斷*

   4. 在 **「診斷設定」** 頁上,選擇您建立的紀錄分析工作區,然後選擇要收集的所有指標並將其發送到 Azure Sentinel。 選取 [儲存]  。

   ![Azure 哨兵連線器設定](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>成本考量
   如果還沒有 Azure 帳戶,則可以創建一個免費帳戶。 轉到[免費帳戶頁面](https://azure.microsoft.com/free/)即可開始,瞭解可以使用免費 Azure 帳戶做什麼,並瞭解哪些產品在 12 個月內是免費的。

   要使用安全功能部署範例應用中的資源,您需要為某些高級功能付費。 隨著應用的擴展以及 Azure 提供的免費層和試用版需要升級以滿足應用程式要求,您的成本可能會增加。 使用 Azure[定價計算機](https://azure.microsoft.com/pricing/calculator/)估計成本。

## <a name="next-steps"></a>後續步驟
   以下文章可説明您設計、開發和部署安全應用程式。

- [設計](secure-design.md)
- [開發](secure-develop.md)
- [部署](secure-deploy.md)
