---
title: 將經典虛擬網路連接到 Azure 資源管理器 VNets：PowerShell
description: 使用 VPN 閘道和 PowerShell 在傳統 VNet 和 Resource Manager VNet 之間建立 VPN 連線。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 1dc0eec6178420976181b05a059e9f8b4859ec2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152001"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>使用 PowerShell 從不同的部署模型連接虛擬網路

本文可協助您將傳統 VNet 連線到 Resource Manager VNet，以允許位於不同部署模型中的資源彼此通訊。 本文中的步驟使用 PowerShell，但您也可以選取這份清單中的文章，進而使用 Azure 入口網站建立此組態。

> [!div class="op_single_selector"]
> * [入口網站](vpn-gateway-connect-different-deployment-models-portal.md)
> * [電源外殼](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

將傳統 VNet 連線至 Resource Manager VNet，類似於將 VNet 連線至內部部署網站位置。 這兩種連線類型都使用 VPN 閘道提供使用 IPsec/IKE 的安全通道。 您可以在不同訂用帳戶和不同區域中的 VNet 之間建立連線。 只要設定的閘道是動態或路由式，您也可以連接已連線到內部部署網路的 Vnet。 如需 VNet 對 VNet 連線的詳細資訊，請參閱本文結尾處的 [VNet 對 VNet 常見問題集](#faq) 。 

如果您還沒有虛擬網路閘道，而且不想建立閘道，可能要考慮改為使用 VNet 對等互連連線您的 VNet。 VNet 對等互連不會使用 VPN 閘道。 如需詳細資訊，請參閱 [VNet 對等互連](../virtual-network/virtual-network-peering-overview.md)。

## <a name="before-you-begin"></a><a name="before"></a>開始之前

下列步驟引導您完成為每個 VNet 設定動態或路由式閘道，以及建立閘道之間的 VPN 連線所需的設定。 此組態不支援靜態或路由式閘道。

### <a name="prerequisites"></a><a name="pre"></a>Prerequisites

* 已建立兩個 Vnet。 如果您需要建立資源管理員虛擬網路，請參閱[建立資源群組和虛擬網路](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network)。 若要建立重統虛擬網路，請參閱[建立傳統 VNet](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic)。
* Vnet 的位址範圍不會彼此重疊，或與閘道可能連接的任何其他連線範圍重疊。
* 您已安裝最新的 PowerShell Cmdlet。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 。 確定安裝服務管理 (SM) 和 Resource Manager (RM) Cmdlet。 

### <a name="example-settings"></a><a name="exampleref"></a>範例設定

您可以使用這些值來建立測試環境，或參考這些值，進一步了解本文中的範例。

**傳統 VNet 設定**

VNet 名稱 = ClassicVNet  <br>
位置 = West US <br>
虛擬網路位址空間 = 10.0.0.0/24 <br>
子網路 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
區域網路名稱 = RMVNetLocal <br>
 GatewayType = DynamicRouting

**Resource Manager VNet 設定**

VNet 名稱 = RMVNet  <br>
資源群組 = RG1 <br>
虛擬網路的 IP 位址空間 = 192.168.0.0/16 <br>
子網路 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
位置 = 美國東部 <br>
閘道公用 IP 名稱 = gwpip <br>
區域網路閘道 = ClassicVNetLocal <br>
虛擬網路閘道名稱 = RMGateway <br>
 閘道 IP 位址組態 = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>區段 1 - 設定傳統 VNet
### <a name="1-download-your-network-configuration-file"></a>1. 下載您的網路設定檔
1. 在 PowerShell 主控台中以提高的權限登入您的 Azure 帳戶。 下列 Cmdlet 會提示您輸入 Azure 帳戶的登入認證。 登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。 本節中會使用傳統服務管理 (SM) Azure PowerShell Cmdlet。

   ```azurepowershell
   Add-AzureAccount
   ```

   取得您的 Azure 訂用帳戶。

   ```azurepowershell
   Get-AzureSubscription
   ```

   如果您有多個訂用帳戶，請選取您要使用的訂用帳戶。

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. 執行下列命令以匯出 Azure 網路組態檔。 您可以視需要變更此檔案的位置，以匯出至不同的位置。

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. 開啟您下載的 .xml 檔案加以編輯。 如需網路組態檔的範例，請參閱 [網路組態結構描述](https://msdn.microsoft.com/library/jj157100.aspx)。

### <a name="2-verify-the-gateway-subnet"></a>2. 驗證閘道子網
在 **VirtualNetworkSites** 元素中，將閘道子網路 (若已建立) 加入至您的 VNet。 使用網路組態檔時，閘道子網路必須命名為 "GatewaySubnet"，否則 Azure 無法辨識並將它當作閘道子網路。

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**例子：**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. 添加本地網路網站
您新增的區域網路站台代表您要連接的 RM VNet。 將 **LocalNetworkSites** 元素 (如果尚未存在) 加入至檔案。 此時在組態中，VPNGatewayAddress 可以是任何有效的公用 IP 位址，因為我們尚未建立 Resource Manager VNet 的閘道。 一旦建立閘道，我們會以指派給 RM 閘道的正確公用 IP 位址取代此預留位置 IP 位址。

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. 將 VNet 與本地網路網站關聯
在此區段中，我們會指定您要 VNet 連接的區域網路站台。 在此例中，這是您稍早參考的 Resource Manager VNet。 確定名稱相符。 此步驟不會建立閘道。 它會指定閘道將要連接的區域網路。

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. 保存檔並上傳
儲存檔案，然後執行下列命令，將它匯入至 Azure。 確定您會視需要變更環境的檔案路徑。

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

您將會看到顯示已成功匯入的相似結果。

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. 創建閘道

執行此範例之前，請參閱您針對 Azure 預期看到的確切名稱所下載的網路組態檔。 網路組態檔包含傳統虛擬網路的值。 因為部署模型中的差異，當在 Azure 入口網站中建立傳統 VNet 設定時，有時候傳統 VNet 的名稱會變更。 例如，如果您使用 Azure 入口網站在名稱為 'ClassicRG' 的資源群組中，建立一個名稱為 'Classic VNet' 的傳統 VNet，網路組態檔中所包含的名稱會轉換為 'Group ClassicRG Classic VNet'。 當指定包含空格的 VNet 名稱時，請使用引號括住值。


使用以下範例來建立動態路由閘道︰

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

您可以使用 **Get-AzureVNetGateway** Cmdlet 檢查閘道的狀態。

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>第 2 節 - 設定 RM VNet 閘道



先決條件會假設您已經建立 RM VNet。 在此步驟中，您會為 RM VNet 建立 VPN 閘道。 請在您已擷取傳統 VNet 閘道的公用 IP 位址之後，才開始下列步驟。 

1. 在 PowerShell 主控台中登入您的 Azure 帳戶。 下列 Cmdlet 會提示您輸入 Azure 帳戶的登入認證。 登入之後，就會下載您的帳戶設定，以供 Azure PowerShell 使用。 您可以視需要使用 [試用] 功能，以在瀏覽器中啟動 Azure Cloud Shell。

   如果您使用 Azure Cloud Shell，請略過下列 Cmdlet：

   ```azurepowershell
   Connect-AzAccount
   ``` 
   若要確認您是否使用正確的訂用帳戶，請執行下列 Cmdlet：  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   如果您有多個訂用帳戶，請指定您要使用的訂用帳戶。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. 建立區域網路閘道。 在虛擬網路中，區域網路閘道通常是指您的內部部署位置。 在此例中，區域網路閘道會參考您的傳統 VNet。 賦予它一個可供 Azure 參考的名稱，並且指定位址空間前置詞。 Azure 會使用您指定的 IP 位址前置詞來識別要傳送至內部部署位置的流量。 如果您稍後需要先在此調整資訊，再建立您的閘道，您可以修改下列值並再次執行範例。
   
   **-Name** 是您要指派給區域網路閘道的參考名稱。<br>
   **-AddressPrefix** 是傳統 VNet 的位址空間。<br>
   **-GatewayIpAddress** 是傳統 VNet 閘道的公用 IP 位址。 請務必變更下列範例文字 "n.n.n.n"，以反映正確的 IP 位址。<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. 要求將公用 IP 位址配置到 Resource Manager VNet 的虛擬網路閘道。 您無法指定想要使用的 IP 位址。 IP 位址是動態地配置到虛擬網路閘道。 但是，這不代表 IP 位址會變更。 只有在刪除及重新建立閘道時，虛擬網路閘道 IP 位址才會變更。 它不會因為重新調整、重設或閘道的其他內部維護/升級而變更。

   在此步驟中，我們也會設定用於後續步驟中的變數。

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. 確認您的虛擬網路有閘道子網路。 如果閘道器子網路不存在，請新增一個。 確定閘道子網路命名為 GatewaySubnet **。
5. 擷取用於閘道的子網路。 在此步驟中，我們也會設定要用於下一個步驟中的變數。
   
   **-Name** 是 Resource Manager VNet 的名稱。<br>
   **-ResourceGroupName** 與 VNet 相關聯的資源群組。 此閘道子網路必須已為此 VNet 存在且命名為 GatewaySubnet ** ，才能正常運作。<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. 建立閘道器 IP 位址組態。 閘道器組態定義要使用的子網路和公用 IP 位址。 使用下列範例來建立閘道組態。

   在本步驟中，**-SubnetId** 和 **-PublicIpAddressId** 參數必須各自從子網路和 IP 位址物件傳遞識別碼屬性。 您無法使用簡單的字串。 這些變數設定於要求公用 IP 的步驟以及擷取子網路的步驟中。

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. 透過執行下列命令建立 Resource Manager 虛擬網路閘道。 `-VpnType` 必須為 *RouteBased*。 閘道建立作業可能需要花費 45 分鐘以上的時間。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. 一旦 VPN 閘道建立好後，複製公用 IP 位址 。 您會在進行傳統 VNet 的區域網路設定時用到它。 您可以使用下列 Cmdlet 來擷取公用 IP 位址。 公用 IP 位址會在傳回資料中列為 IpAddress **。

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>第 3 節 - 修改傳統 VNet 本機站台設定

在本節中，您會處理傳統 VNet。 您會取代您在指定本機站台設定 (將用於連線至 Resource Manager VNet 閘道) 時所使用的預留位置 IP 位址。 由於您目前使用的是傳統 VNet，因此請使用安裝在您電腦本機的 PowerShell，而不是 Azure Cloud Shell TryIt。

1. 匯出網路組態檔。

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. 使用文字編輯器修改 VPNGatewayAddress 的值。 使用 Resource Manager 閘道的公用 IP 位址取代預留位置 IP 位址，然後儲存變更。

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. 將修改過的網路組態檔匯入至 Azure。

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>第 4 節 - 在閘道之間建立連線
在閘道之間建立連線需要 PowerShell。 您可能需要新增 Azure 帳戶，才能使用傳統版 PowerShell Cmdlet。 若要這麼做，請使用 **Add-azureaccount**。

1. 在 PowerShell 主控台中，設定您的共用金鑰。 執行 Cmdlet 之前，請參閱您針對 Azure 預期看到的確切名稱所下載的網路組態檔。 當指定包含空格的 VNet 名稱時，請使用單引號括住值。<br><br>在下列範例中，**-VNetName** 是傳統 VNet 的名稱，**-LocalNetworkSiteName** 是您為區域網路網站指定的名稱。 **-共用金鑰**是您生成和指定的值。 在範例中，我們使用的是 'abc123'，但是您可以產生並使用更為複雜的值。 重要的是，您在此指定的值必須與您在下一個步驟中建立連線時指定的值相同。 傳回應顯示 [狀態: 成功]****。

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. 執行下列命令來建立 VPN 連線：
   
   設定變數。

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   建立連線。 請注意，**-ConnectionType** 是 IPsec，而不是 Vnet2Vnet。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>第 5 節 - 驗證您的連線

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>驗證從傳統 VNet 到 Resource Manager VNet 的連線

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure 入口網站

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>驗證從 Resource Manager VNet 到傳統 VNet 的連線

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure 入口網站

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>VNet 對 VNet 常見問題集

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
