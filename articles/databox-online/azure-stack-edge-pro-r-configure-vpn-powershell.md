---
title: 使用 Azure PowerShell 在您的 Azure Stack Edge Pro R 裝置上設定 VPN
description: 說明如何使用 Azure PowerShell 腳本來建立 Azure 資源，以在您的 Azure Stack Edge Pro R 裝置上設定 VPN。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 2139080367cdce9a5f018afab0970a7bd0e7504c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466251"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>透過 Azure PowerShell 在您的 Azure Stack Edge Pro R 裝置上設定 VPN

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN 選項會針對從 Azure Stack Edge Pro R 裝置到 Azure 的 TLS，提供從 *TLS* 對資料進行的第二層加密。 您可以透過 Azure 入口網站或 Azure PowerShell，在 Azure Stack Edge Pro R 裝置上設定 VPN。

本文說明使用 Azure PowerShell 在雲端中建立設定的 Azure Stack Edge Pro R 裝置上設定 VPN 所需的步驟。

## <a name="about-vpn-setup"></a>關於 VPN 設定

跨單位 VPN 連線是由 Azure VPN 閘道、內部部署 VPN 裝置以及讓兩者相連的 IPsec S2S VPN 通道所組成。 典型的工作流程包含下列步驟：

- 設定必要條件。
- 在 Azure 上設定必要的資源。
    -  (虛擬網路閘道) 建立及設定 Azure VPN 閘道。
    - 建立並設定 Azure 局域網路閘道，以代表您的內部部署網路和 VPN 裝置。
    - 建立並設定 Azure VPN 閘道與局域網路閘道之間的 Azure VPN 連線。
    - 設定 Azure 防火牆，並新增網路和應用程式規則。
    - 建立 Azure 路由表並新增路由。
- 在裝置的本機 web UI 中設定 VPN。 您可以設定局域網路閘道表示的內部部署 VPN 裝置，以使用 Azure VPN 閘道來建立實際的 S2S VPN 通道。

下列各節提供詳細步驟。

## <a name="configure-prerequisites"></a>設定必要條件

1. 您應該可以根據 [安裝 Azure Stack Edge Pro r 裝置](azure-stack-edge-pro-r-deploy-install.md)中的指示，存取安裝的 Azure Stack Edge Pro r 裝置。 此裝置將作為內部部署 VPN 裝置，以建立與 Azure 的 VPN 連線。 

2. 您的 VPN 裝置應該有 (外部) 的靜態公用 IP 位址。 此位址不應該是 NAT。

3. 您應該能夠存取在 Azure 中啟用 Azure Stack Edge 服務的有效 Azure 訂用帳戶。 使用此訂用帳戶在 Azure 中建立對應的資源，以管理您的 Azure Stack Edge Pro R 裝置。  

4. 您可以存取您將用來存取 Azure Stack Edge Pro R 裝置的 Windows 用戶端。 您將使用此用戶端以程式設計方式建立雲端中的設定。

    1. 若要在您的 Windows 用戶端上安裝所需版本的 PowerShell，請執行下列命令：

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. 若要連接到您的 Azure 帳戶和訂用帳戶，請執行下列命令：

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        提供您要搭配 Azure Stack Edge Pro R 裝置使用的 Azure 訂用帳戶名稱來設定 VPN。

    3. 下載在雲端中建立設定所需[的腳本](https://aka.ms/ase-vpn-deployment)。 此指令碼會：
        
        - 建立 Azure 虛擬網路和下列子網： *GatewaySubnet* 和 *AzureFirewallSubnet*。
        - 建立並設定 Azure VPN 閘道。
        - 建立並設定 Azure 局域網路閘道。
        - 建立並設定 Azure VPN 閘道與局域網路閘道之間的 Azure VPN 連線。
        - 建立 Azure 防火牆，並新增網路規則和應用程式規則。
        - 建立 Azure 路由表，並將路由新增至該資料表。


## <a name="use-the-script"></a>使用腳本

首先您要修改檔案 `parameters.json` ，以輸入您的參數。 接下來，您會使用修改過的 json 檔案來執行腳本。

下列各節將討論這些步驟。

### <a name="download-service-tags-file"></a>下載服務標記檔案

您在 `ServiceTags.json` 下載腳本的資料夾中可能已經有檔案。 如果沒有，您可以下載服務標記檔案。

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>修改參數檔案

第一個步驟是修改檔案 `parameters.json` 並儲存變更。 


針對您所建立的 Azure 資源，您將提供下列名稱：

|參數名稱  |描述  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure 虛擬網路名稱        |
|azureFirewalls_firewall_name     | Azure 防火牆名稱        |
|routeTables_routetable_name     | Azure 路由表名稱        |
|publicIPAddresses_VNGW_public_ip_name     | 虛擬網路閘道的公用 IP 位址名稱       |
|virtualNetworkGateways_VNGW_name    | Azure VPN 閘道 (虛擬網路閘道) 名稱        |
|publicIPAddresses_firewall_public_ip_name     | Azure 防火牆的公用 IP 位址名稱         |
|localNetworkGateways_LNGW_name    |Azure 局域網路閘道名稱          |
|connections_vngw_lngw_name    | Azure VPN 連接名稱。 這是您的虛擬網路閘道與局域網路閘道之間的連線。       |
|location     |這是您要在其中建立虛擬網路的區域。 選取與您的裝置相關聯的區域。         |

下列 IP 位址和位址空間適用于所建立的 Azure 資源，包括虛擬網路和相關聯的子網 (預設、防火牆、GatewaySubnet) 。

|參數名稱  |描述  |
|---------|---------|
|VnetIPv4AddressSpace    | 這是與您的虛擬網路相關聯的位址空間。       |
|DefaultSubnetIPv4AddressSpace    |這是與 `Default` 您虛擬網路的子網相關聯的位址空間。         |
|FirewallSubnetIPv4AddressSpace    |這是與 `Firewall` 您虛擬網路的子網相關聯的位址空間。          |
|GatewaySubnetIPv4AddressSpace    |這是與您的虛擬網路相關聯的位址空間 `GatewaySubnet` 。          |
|GatewaySubnetIPv4bgpPeeringAddress    | 這是保留給 BGP 通訊的 IP 位址，並以與您虛擬網路的相關聯的位址空間為基礎 `GatewaySubnet` 。          |

下列 IP 位址和位址空間與內部部署網路有關， () 部署 Azure Stack Edge Pro R 裝置。

|參數名稱  |描述  |
|---------|---------|
|CustomerNetworkAddressSpace    |  這是私人 IP 位址的位址空間。       |
|CustomerPublicNetworkAddressSpace    |  這是您公用 IP 位址的位址空間。       |
|DbeIOTNetworkAddressSpace    |IoT 服務會保留此 IP 位址。 請勿變更此參數。        |
|AzureVPNsharedKey    |在建立 Azure VPN 連線資源期間，會使用此共用金鑰。 在本機 web UI VPN 設定期間，也會以 VPN 共用密碼的形式提供此金鑰。         |
|DBE-閘道-ipaddress   | 您 Azure Stack Edge Pro R 裝置的公用 IP 位址。 這可能不是已知的，且您可以使用預留位置 IP 位址來執行腳本。 使用實際的 IP 位址，稍後再編輯局域網路閘道。     |

#### <a name="caveats-to-keep-in-mind"></a>請注意下列事項：

- 您將不會有 Azure Stack Edge Pro R 裝置的 IP 位址。 您可以使用預留位置 IP 位址來建立資源，並于稍後修改 Azure 局域網路閘道，以指派裝置的實際裝置 IP 位址和局域網路的位址空間。
- 根據網際網路指派號碼授權單位上 IETF 的 (IANA) ，使用從 >172.16. 到172.24 的任何子網。 z.a。 我們保留 Azure 網路的 172.24 IPv4 位址範圍。

### <a name="run-the-script"></a>執行指令碼

請遵循下列步驟，使用修改後 `parameters.json` 執行腳本來建立 Azure 資源。

1. 以系統管理員身分執行 PowerShell。

2. 切換至腳本所在的目錄。

3. 執行指令碼。

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    下方顯示一項範例輸出。

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    腳本需要大約90分鐘的時間來執行。 腳本完成之後，就會在腳本所在的相同資料夾中產生部署記錄檔。


## <a name="verify-the-azure-resources"></a>驗證 Azure 資源

順利執行腳本之後，請確認已在 Azure 中建立所有資源。

接下來，您將在裝置的本機 web UI 上設定 VPN。


## <a name="vpn-configuration-on-the-device"></a>裝置上的 VPN 設定

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>驗證 VPN

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>驗證透過 VPN 的資料傳輸

若要確認 VPN 是否正常運作，請將資料複製到 SMB 共用。 遵循在您的 Azure Stack Edge Pro R 裝置上 [新增共用](azure-stack-edge-j-series-manage-shares.md#add-a-share) 的步驟。 

1. 複製檔案，例如 \data\pictures\waterfall.jpg 到您掛接在用戶端系統上的 SMB 共用。 
2. 確認此檔案顯示在雲端的儲存體帳戶中。

若要驗證資料是否通過 VPN：

1. 開啟資源群組中存在的連線資源。 

2. 檢查資料中的資料和資料的輸出值。
 
3. 開啟資源群組中的虛擬網路閘道。 查看 **所有** 通道 **輸入和通道輸出總計的** 圖表。
 
## <a name="debug-issues"></a>為問題偵錯

若要偵測任何問題，請使用下列命令：

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

範例輸出如下所示：


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>後續步驟

[透過 Azure Stack Edge Pro R 裝置上的本機 UI 來設定 VPN](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
