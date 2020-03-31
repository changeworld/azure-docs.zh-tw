---
title: Azure VPN 閘道：將閘道連接到多個基於本地策略的 VPN 設備
description: 使用 Azure Resource Manager 和 PowerShell，將以 Azure 路由為基礎的 VPN 閘道設定為多個以原則為基礎的 VPN 裝置。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: yushwang
ms.openlocfilehash: 687c33e50a986cf8af08d0201fe0159a79cf02a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123328"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>使用 PowerShell 將 Azure VPN 閘道連線至多個內部部署以原則為基礎的 VPN 裝置

本文將協助您運用 S2S VPN 連線上的自訂 IPsec/IKE 原則，設定以 Azure 路由為基礎的 VPN 閘道連線至多個內部部署以原則為基礎的 VPN 裝置。

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>關於以原則為基礎的 VPN 閘道和以路由為基礎的 VPN 閘道

基於策略*的 VPN*設備與基於路由的 VPN 設備在連接上設置 IPsec 流量選擇器的方式不同：

* **以原則為基礎的** VPN 裝置使用這兩個網路的前置詞組合，定義如何透過 IPsec 通道來加密/解密流量。 它通常內建在執行封包篩選的防火牆裝置上。 IPsec 通道加密和解密會新增至封包篩選和處理引擎。
* **以路由為基礎的** VPN 裝置使用任何對任何 (萬用字元) 流量選取器，並讓路由/轉接資料表將流量導向到不同 IPsec 通道。 它通常內建在路由器平台上，其中，每個 IPsec 通道都會建模為網路介面或 VTI (虛擬通道介面)。

下列各圖反白顯示兩個模型：

### <a name="policy-based-vpn-example"></a>以原則為基礎的 VPN 範例
![以原則為基礎](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>以路由為基礎的 VPN 範例
![以路由為基礎](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>以原則為基礎的 VPN 的 Azure 支援
Azure 目前支援兩種 VPN 閘道模式：以路由為基礎的 VPN 閘道和以原則為基礎的 VPN 閘道。 它們內建在不同內部平台上，因而導致不同的規格：

|                          | **PolicyBased VPN 閘道** | **基於路由的 VPN 閘道**       |**基於路由的 VPN 閘道**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **Azure 閘道 SKU**    | 基本                       | 基本                            | VpnGw1， VpnGw2， VpnGw3， VpnGw4， VpnGw5  |
| **IKE 版本**          | IKEv1                       | IKEv2                            | IKEv1 和 IKEv2                         |
| **麥克斯。S2S 連接** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

您現在可以使用自訂 IPsec/IKE 原則，設定以 Azure 路由為基礎的 VPN 閘道搭配使用以前置詞為基礎的流量選取器與 "**PolicyBasedTrafficSelectors**" 選項，以連線至內部部署以原則為基礎的 VPN 裝置。 這項功能可讓您從 Azure 虛擬網路和 VPN 閘道連線至多個內部部署以原則為基礎的 VPN/防火牆裝置，並從目前以 Azure 原則為基礎的 VPN 閘道移除單一連線限制。

> [!IMPORTANT]
> 1. 若要啟用這個連線，內部部署以原則為基礎的 VPN 裝置必須支援 **IKEv2** 連線至以 Azure 路由為基礎的 VPN 閘道。 確認您的 VPN 裝置規格。
> 2. 使用這種機制透過以原則為基礎的 VPN 裝置所連線的內部部署網路，只能連線至 Azure 虛擬網路；**其無法透過相同的 Azure VPN 閘道轉換到其他內部部署網路或虛擬網路**。
> 3. 設定選項是自訂 IPsec/IKE 連線原則的一部分。 如果您啟用以原則為基礎的流量選取器選項，則必須指定完整原則 (IPsec/IKE 加密及完整性演算法、金鑰長度和 SA 存留期)。

下圖顯示透過 Azure VPN 閘道的轉換路由為何不適用於以原則為基礎的選項：

![以原則為基礎的傳輸](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

如圖所示，Azure VPN 閘道會有從虛擬網路到每個內部部署網路前置詞的流量選取器，但不是跨連線前置詞。 例如，內部部署網站 2、網站 3 和網站 4 分別可以與 VNet1 通訊，但無法透過 Azure VPN 閘道彼此連線。 此圖顯示此設定下不適用於 Azure VPN 閘道的跨連線流量選取器。

## <a name="workflow"></a><a name="workflow"></a>工作流程

本文中的指示遵循[設定 S2S 或 VNet 對 VNet 連線的 IPsec/IKE 原則](vpn-gateway-ipsecikepolicy-rm-powershell.md)中所述的相同範例，以建立 S2S VPN 連線。 如下圖所示：

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

啟用這個連線的工作流程：
1. 為跨界連接創建虛擬網路、VPN 閘道和本地網路閘道。
2. 創建 IPsec/IKE 策略。
3. 創建 S2S 或 VNet 到 VNet 連接時應用策略，並在連接上**啟用基於策略的流量選擇器**。
4. 如果連接已創建，則可以將策略應用於現有連接或更新策略。

## <a name="before-you-begin"></a>開始之前

* 請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂閱，則可以啟動[MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>啟用基於策略的流量選擇器

本節介紹如何在連接上啟用基於策略的流量選擇器。 請確保您已完成[配置 IPsec/IKE 策略文章的第 3 部分](vpn-gateway-ipsecikepolicy-rm-powershell.md)。 本文中的步驟使用相同的參數。

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>步驟1 - 建立虛擬網路、VPN 閘道和區域網路閘道

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>連線至訂用帳戶並宣告變數

1. 如果您在電腦上本地運行 PowerShell，請使用*Connect-AzAccount* Cmdlet 登錄。 或者，在瀏覽器中使用 Azure 雲外殼。

2. 宣告變數。 針對此練習，我們使用下列變數：

   ```azurepowershell-interactive
   $Sub1          = "<YourSubscriptionName>"
   $RG1           = "TestPolicyRG1"
   $Location1     = "East US 2"
   $VNetName1     = "TestVNet1"
   $FESubName1    = "FrontEnd"
   $BESubName1    = "Backend"
   $GWSubName1    = "GatewaySubnet"
   $VNetPrefix11  = "10.11.0.0/16"
   $VNetPrefix12  = "10.12.0.0/16"
   $FESubPrefix1  = "10.11.0.0/24"
   $BESubPrefix1  = "10.12.0.0/24"
   $GWSubPrefix1  = "10.12.255.0/27"
   $DNS1          = "8.8.8.8"
   $GWName1       = "VNet1GW"
   $GW1IPName1    = "VNet1GWIP1"
   $GW1IPconf1    = "gw1ipconf1"
   $Connection16  = "VNet1toSite6"
   $LNGName6      = "Site6"
   $LNGPrefix61   = "10.61.0.0/16"
   $LNGPrefix62   = "10.62.0.0/16"
   $LNGIP6        = "131.107.72.22"
   ```

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>建立虛擬網路、VPN 閘道和區域網路閘道

1. 建立資源群組。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. 使用下列範例來建立有三個子網路的虛擬網路 TestVNet1 和 VPN 閘道。 如果您想要替代值，請務必將閘道子網路命名為 'GatewaySubnet'。 如果您將其命名為其他名稱，閘道建立會失敗。

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>步驟 2 - 使用 IPsec/IKE 策略創建 S2S VPN 連接

1. 創建 IPsec/IKE 策略。

   > [!IMPORTANT]
   > 您需要建立 IPsec/IKE 原則，才能在連線上啟用 "UsePolicyBasedTrafficSelectors" 選項。

   下列範例會使用這些演算法和參數來建立 IPsec/IKE 原則：
    * IKEv2：AES256、SHA384、DHGroup24
    * IPsec：AES256、SHA256、PFS 無、SA 存留期 14400 秒和 102400000 KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. 使用基於策略的流量選擇器和 IPsec/IKE 策略創建 S2S VPN 連接，並應用在上一步中創建的 IPsec/IKE 策略。 請注意附加參數"-基於策略的流量選擇器$True"，它支援連接上基於策略的流量選擇器。

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. 完成步驟後，S2S VPN 連線將會使用所定義的 IPsec/IKE 原則，並在連線上啟用以原則為基礎的流量選取器。 您可以重複相同的步驟，以從相同的 Azure VPN 閘道將更多的連線新增至其他內部部署以原則為基礎的 VPN 裝置。

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>更新基於策略的流量選擇器
本節介紹如何更新現有 S2S VPN 連接的基於策略的流量選擇器選項。

1. 取得連線資源。

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. 查看基於策略的流量選擇器選項。
下行將說明是否將以原則為基礎的流量選取器用於連線：

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   如果該行傳回 "**True**"，則已在連線上設定以原則為基礎的流量選取器；否則，它會傳回 "**False**"。
1. 獲取連接資源後，可以在連接上啟用或禁用基於策略的流量選擇器。

   - 啟用

      下列範例會啟用以原則為基礎的流量選取器選項，但 IPsec/IKE 原則保留不變：

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - 禁用

      下列範例會停用以原則為基礎的流量選取器選項，但 IPsec/IKE 原則保留不變：

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>後續步驟
一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 請參閱 [建立網站的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 以取得相關步驟。

如需自訂 IPsec/IKE 原則的詳細資訊，也請檢閱[設定 S2S VPN 或 VNet 對 VNet 連線的 IPsec/IKE 原則](vpn-gateway-ipsecikepolicy-rm-powershell.md)。
