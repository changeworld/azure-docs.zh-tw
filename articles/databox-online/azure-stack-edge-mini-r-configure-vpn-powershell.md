---
title: 使用 Azure PowerShell 在您的 Azure Stack Edge 迷你 R 裝置上設定 VPN
description: 說明如何使用 Azure PowerShell 腳本來建立 Azure 資源，以在您的 Azure Stack Edge 迷你 R 裝置上設定 VPN。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 763ccd397d8cd704ca161032e65f17979bccb53b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466402"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>透過 Azure PowerShell 在您的 Azure Stack Edge 迷你 R 裝置上設定 VPN

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN 選項會針對從 Azure Stack Edge 迷你 R 或 Azure Stack Edge Pro R 裝置到 Azure 的 TLS，提供從 *TLS* 對資料進行的第二層加密。 您可以透過 Azure 入口網站或透過 Azure PowerShell，在 Azure Stack Edge 迷你 R 裝置上設定 VPN。 

本文說明在 Azure Stack Edge 迷你 R 裝置上設定點對站 (P2S) VPN 的必要步驟，使用 Azure PowerShell 腳本在雲端中建立設定。 Azure Stack Edge 裝置上的設定是透過本機 UI 來完成。

## <a name="about-vpn-setup"></a>關於 VPN 設定

P2S VPN 閘道連線可讓您建立從個別用戶端電腦或 Azure Stack Edge 迷你 R 裝置到虛擬網路的安全連線。 您可以從用戶端電腦或裝置啟動 P2S 連接。 在此情況下，P2S 連線會使用 IKEv2 VPN （以標準為基礎的 IPsec VPN 解決方案）。

典型的工作流程包含下列步驟：

1. 設定必要條件。
2. 在 Azure 上設定必要的資源。
    1. 建立並設定虛擬網路和必要的子網。 
    2.  (虛擬網路閘道) 建立及設定 Azure VPN 閘道。
    3. 設定 Azure 防火牆，並新增網路和應用程式規則。
    4. 建立 Azure 路由表並新增路由。
    5. 啟用 VPN 閘道中的點對站。
        1. 新增用戶端位址集區。
        2. 設定通道類型。
        3. 設定驗證類型。
        4. 建立憑證。
        5. 上傳憑證。
    6. 下載電話簿。
3. 在裝置的本機 web UI 中設定 VPN。 
    1. 提供電話簿。
    2.  (json) 檔中提供服務標記。


下列各節提供詳細步驟。

## <a name="configure-prerequisites"></a>設定必要條件

- 您應該可以根據 [安裝 Azure Stack Edge 迷你 r 裝置](azure-stack-edge-mini-r-deploy-install.md)中的指示，存取安裝的 Azure Stack Edge 迷你 r 裝置。 此裝置將會建立與 Azure 的 P2S 連線。 

- 您應該能夠存取在 Azure 中啟用 Azure Stack Edge 服務的有效 Azure 訂用帳戶。 使用此訂用帳戶在 Azure 中建立對應的資源，以管理您的 Azure Stack Edge 迷你 R 裝置。  

- 您可以存取您將用來存取 Azure Stack Edge 迷你 R 裝置的 Windows 用戶端。 您將使用此用戶端以程式設計方式建立雲端中的設定。

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
        使用您的 Azure Stack Edge 迷你 R 裝置來設定 VPN，以提供您所使用的 Azure 訂用帳戶名稱。

    3. 下載在雲端中建立設定所需[的腳本](https://aka.ms/ase-vpn-deployment)。 此指令碼會：
        
        - 建立 Azure 虛擬網路和下列子網： *GatewaySubnet* 和 *AzureFirewallSubnet*。
        - 建立並設定 Azure VPN 閘道。
        - 建立並設定 Azure 局域網路閘道。
        - 建立並設定 Azure VPN 閘道與局域網路閘道之間的 Azure VPN 連線。
        - 建立 Azure 防火牆，並新增網路規則和應用程式規則。
        - 建立 Azure 路由表，並將路由新增至該資料表。

    4. 在您想要建立 Azure 資源的 Azure 入口網站中建立資源群組。 移至 Azure 入口網站中的服務清單，選取 [ **資源群組** ]，然後選取 [ **+ 新增**]。 提供訂用帳戶資訊和資源群組的名稱，然後選取 [ **建立**]。 如果您移至此資源群組，此時它不應該有任何資源。

        ![Azure 資源群組](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. 您必須為 `.cer` 您 Azure Stack Edge 迷你 R 裝置的格式採用 Base 64 編碼的憑證。 此憑證應隨私密金鑰上傳至您的 Azure Stack Edge 裝置 `pfx` 。 此憑證也必須安裝在嘗試建立 P2S 連線之用戶端上存放區的根信任目錄中。

## <a name="use-the-script"></a>使用腳本

首先您要修改檔案 `parameters-p2s.json` ，以輸入您的參數。 接下來，您會使用修改過的 json 檔案來執行腳本。

下列各節將討論這些步驟。

### <a name="download-service-tags-file"></a>下載服務標記檔案

您在 `ServiceTags.json` 下載腳本的資料夾中可能已經有檔案。 如果沒有，您可以下載服務標記檔案。

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>修改參數檔案

第一個步驟是修改檔案 `parameters-p2s.json` 並儲存變更。 

針對您所建立的 Azure 資源，您將提供下列名稱：

|參數名稱  |描述  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure 虛擬網路名稱        |
|azureFirewalls_firewall_name     | Azure 防火牆名稱        |
|routeTables_routetable_name     | Azure 路由表名稱        |
|publicIPAddresses_VNGW_public_ip_name     | 虛擬網路閘道的公用 IP 位址名稱       |
|virtualNetworkGateways_VNGW_name    | Azure VPN 閘道 (虛擬網路閘道) 名稱        |
|publicIPAddresses_firewall_public_ip_name     | Azure 防火牆的公用 IP 位址名稱         |
|location     |這是您要在其中建立虛擬網路的區域。 選取與您的裝置相關聯的區域。         |
|RouteTables_routetable_onprem_name| 這是其他路由表的名稱，可協助防火牆將封包路由回 Azure Stack Edge 裝置。 此腳本會建立兩個額外的路由，並將 *預設* 和 *FirewallSubnet* 與此路由表建立關聯。|

為所建立的 Azure 資源提供下列 IP 位址和位址空間，包括虛擬網路和相關聯的子網 (*預設*、 *防火牆*、 *GatewaySubnet*) 。

|參數名稱  |描述  |
|---------|---------|
|VnetIPv4AddressSpace    | 這是與您的虛擬網路相關聯的位址空間。 提供作為私人 IP 範圍 (的 Vnet IP 範圍 https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) 。     |
|DefaultSubnetIPv4AddressSpace    |這是與 `Default` 您虛擬網路的子網相關聯的位址空間。         |
|FirewallSubnetIPv4AddressSpace    |這是與 `Firewall` 您虛擬網路的子網相關聯的位址空間。          |
|GatewaySubnetIPv4AddressSpace    |這是與您的虛擬網路相關聯的位址空間 `GatewaySubnet` 。          |
|GatewaySubnetIPv4bgpPeeringAddress    | 這是保留給 BGP 通訊的 IP 位址，並以與您虛擬網路的相關聯的位址空間為基礎 `GatewaySubnet` 。          |
|ClientAddressPool    | 此 IP 位址會用於 Azure 入口網站中 P2S 設定的位址集區。         |
|PublicCertData     | VPN 閘道會使用公開憑證資料來驗證與其連接的 P2S 用戶端。 若要取得憑證資料，請安裝根憑證。 請確定憑證的 Base-64 編碼為 .cer 副檔名。 開啟這個憑證，並將憑證中的文字複製到一個連續一行的 = = BEGIN CERTIFICATE = = 和 = = END CERTIFICATE = =。     |



### <a name="run-the-script"></a>執行指令碼

請遵循下列步驟，使用修改後 `parameters-p2s.json` 執行腳本來建立 Azure 資源。

1. 執行 PowerShell。 切換至腳本所在的目錄。

3. 執行指令碼。

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > 在此版本中，腳本只會在美國東部位置運作。

    當您執行腳本時，您將需要輸入下列資訊：

    
    |參數  |描述  |
    |---------|---------|
    |Location     |這是必須在其中建立 Azure 資源的區域。         |
    |AzureAppRuleFilePath     | 這是的檔案路徑 `appRule.json` 。       |
    |AzureIPRangesFilePath     |這是您在先前步驟中下載的服務標記 json 檔案。         |
    |resourceGroupName     | 這是用來建立所有 Azure 資源的資源組名。        |
    |AzureDeploymentName    |這是您 Azure 部署的名稱。         |
    |NetworkRuleCollectionName            | 這是建立並新增至 Azure 防火牆的所有網路規則集合的名稱。             |
    |優先順序            | 這是指派給所有已建立之網路和應用程式規則的優先順序。              |
    |AppRuleCollectionName            |這是建立並新增至 Azure 防火牆的所有應用程式規則集合的名稱。                |


    下方顯示一項範例輸出。
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - 腳本需要大約90分鐘的時間來執行。 在腳本啟動之前，請務必先登入您的網路。
    > - 如果因為任何原因而腳本發生失敗的會話，請務必刪除資源群組，以刪除在其下建立的所有資源。
  
    
    腳本完成之後，就會在腳本所在的相同資料夾中產生部署記錄檔。


## <a name="verify-the-azure-resources"></a>驗證 Azure 資源

順利執行腳本之後，請確認已在 Azure 中建立所有資源。 移至您建立的資源群組。 您應該會看到下列資源：

![Azure 資源](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>下載 VPN 設定檔的電話簿

在此步驟中，您將下載您裝置的 VPN 設定檔。

1. 在 Azure 入口網站中，移至資源群組，然後選取您在先前步驟中建立的虛擬網路閘道。

    ![Azure 虛擬網路閘道](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. 移至 [ **設定] > 點對站設定**。 選取 [ **下載 VPN 用戶端**]。

    ![啟用 P2S 設定1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. 儲存壓縮的設定檔，並在您的 Windows 用戶端上解壓縮。

    ![啟用 P2S 設定2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. 移至 [ *WindowsAmd64* ] 資料夾，然後解壓縮 `.exe` ： *VpnClientSetupAmd64.exe*。

    ![啟用 P2S 設定3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. 建立暫存路徑。 例如：

    `C:\NewTemp\vnet\tmp`

4. 執行 PowerShell 並移至所在的目錄 `.exe` 。 若要執行 `.exe` ，請輸入：

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. 暫存路徑將會有新的檔案。 以下是範例輸出：

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. *.Pbk* 檔案是 VPN 設定檔的電話簿。 您將在本機 UI 中使用此功能。


## <a name="vpn-configuration-on-the-device"></a>裝置上的 VPN 設定

請在 Azure Stack Edge 裝置的本機 UI 上遵循下列步驟。

1. 在本機 UI 中，移至 [ **VPN** ] 頁面。 在 [VPN 狀態] 底下，選取 [ **設定**]。

    ![設定 VPN 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. 在 [ **設定 VPN** ] 分頁中：
    
    1. 在 [上傳電話簿] 檔案中，指向您在先前步驟中建立的 .pbk 檔案。
    2. 在上傳公用 IP 清單設定檔中，提供 Azure 資料中心 IP 範圍 JSON 檔案作為輸入。 您已在先前的步驟中下載此檔案，來源為： [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) 。
    3. 選取 [ **eastus** ] 作為區域，然後選取 [套用 **]。**

    ![設定 VPN 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. 在 [ **僅使用 VPN 存取的 IP 位址範圍** ] 區段中，輸入您為 Azure 虛擬網路選擇的 Vnet IPv4 範圍。

    ![設定 VPN 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>確認用戶端連接

1. 在 Azure 入口網站中，移至 VPN 閘道。
2. 移至 [ **設定] > 點對站設定**。 在 [已配置的 **IP 位址**] 下，應該會顯示 Azure Stack Edge 裝置的 ip 位址。

## <a name="validate-data-transfer-through-vpn"></a>驗證透過 VPN 的資料傳輸

若要確認 VPN 是否正常運作，請將資料複製到 SMB 共用。 遵循在您的 Azure Stack Edge 裝置上 [新增共用](azure-stack-edge-j-series-manage-shares.md#add-a-share) 的步驟。 

1. 複製檔案，例如 \data\pictures\waterfall.jpg 到您掛接在用戶端系統上的 SMB 共用。 
2. 若要驗證資料是否通過 VPN，正在複製資料：

    1. 移至 Azure 入口網站中的 VPN 閘道。 

    2. 移至 [ **監視] > 計量**。

    3. 在右窗格中，選擇 [作為 VPN 閘道的 **範圍** ]、[閘道 P2S 頻寬的 **度量** ] 和 [ **匯總** 為平均]。

    4. 複製資料時，您會看到頻寬使用率增加，而且當資料複製完成時，頻寬使用率將會下降。

        ![Azure vpn 計量](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. 確認此檔案顯示在雲端的儲存體帳戶中。
 
## <a name="debug-issues"></a>為問題偵錯

若要偵測任何問題，請使用下列命令：

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

範例輸出如下所示：


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
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

透過[Azure Stack Edge 裝置上的本機 UI 來設定 VPN](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)。