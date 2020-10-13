---
title: 設定 ExpressRoute 加密：適用于 Azure 虛擬 WAN 的 IPsec over ExpressRoute
description: 在本教學課程中，瞭解如何使用 Azure 虛擬 WAN 透過 ExpressRoute 私用對等互連來建立站對站 VPN 連線。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: bbce84ad917da71ab363b20f3aef9da79ed3f2b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827951"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute encryption：透過 ExpressRoute 進行虛擬 WAN 的 IPsec

本文說明如何使用 Azure 虛擬 WAN，透過 Azure ExpressRoute 線路的私人對等互連，來建立從內部部署網路到 Azure 的 IPsec/IKE VPN 連線。 這項技術可透過 ExpressRoute 在內部部署網路與 Azure 虛擬網路之間提供加密的傳輸，而不需要經過公用網際網路或使用公用 IP 位址。

## <a name="topology-and-routing"></a>拓撲和路由

下圖顯示透過 ExpressRoute 私用對等互連的 VPN 連線範例：

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="透過 ExpressRoute 的 VPN":::

此圖顯示透過 ExpressRoute 私用對等互連連線到 Azure hub VPN 閘道的內部部署網路內的網路。 連線建立很簡單：

1. 使用 ExpressRoute 線路和私用對等互連來建立 ExpressRoute 連線能力。
2. 如本文所述，建立 VPN 連線能力。

這項設定的重要層面是在內部部署網路與 Azure 之間，透過 ExpressRoute 和 VPN 路徑進行路由。

### <a name="traffic-from-on-premises-networks-to-azure"></a>從內部部署網路到 Azure 的流量

針對從內部部署網路到 Azure 的流量，Azure 首碼 (包括虛擬中樞，以及連線至中樞) 的所有輪輻虛擬網路都是透過 ExpressRoute 私用對等互連 BGP 和 VPN BGP 來公告。 這會導致兩個網路路由從內部部署網路) 到 Azure (路徑：

- 一個 over 受 IPsec 保護的路徑
- 一個直接透過 *沒有* IPsec 保護的 ExpressRoute 

若要將加密套用至通訊，您必須確定在圖表中，透過內部部署 VPN 閘道的 Azure 路由優先于直接 ExpressRoute 路徑。

### <a name="traffic-from-azure-to-on-premises-networks"></a>從 Azure 到內部部署網路的流量

相同需求適用于從 Azure 到內部部署網路的流量。 若要確保在沒有 IPsec) 的情況下， (直接 ExpressRoute 路徑的 IPsec 路徑，您有兩個選項：

- 在 vpn 連線網路的 VPN BGP 會話上通告更明確的首碼。 您可以透過 ExpressRoute 私用對等互連來公告包含 VPN 連線網路的較大範圍，然後在 VPN BGP 會話中提供更具體的範圍。 例如，透過 ExpressRoute 公告 10.0.0.0/16，並透過 VPN 通告 10.0.1.0/24。

- 公告 VPN 和 ExpressRoute 的脫離首碼。 如果 VPN 連線的網路範圍與其他 ExpressRoute 連線網路不相鄰，您可以分別在 VPN 和 ExpressRoute BGP 會話中通告首碼。 例如，透過 ExpressRoute 公告 10.0.0.0/24，並透過 VPN 通告 10.0.1.0/24。

在這兩個範例中，Azure 會透過 VPN 連線將流量傳送至 10.0.1.0/24，而不是直接透過沒有 VPN 保護的 ExpressRoute。

> [!WARNING]
> 如果您在 ExpressRoute 和 VPN 連線上公告 *相同* 的首碼，Azure 將直接使用 expressroute 路徑，而不使用 vpn 保護。
>

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. 使用閘道建立虛擬 WAN 和中樞

在您繼續之前，必須先備妥下列 Azure 資源和對應的內部部署設定：

- Azure 虛擬 WAN
- 具有[ExpressRoute 網](virtual-wan-expressroute-portal.md)關和[VPN 閘道](virtual-wan-site-to-site-portal.md)的虛擬 WAN 中樞

如需建立 Azure 虛擬 WAN 和具有 ExpressRoute 關聯的中樞的步驟，請參閱 [使用 Azure 虛擬 WAN 建立 expressroute 關聯](virtual-wan-expressroute-portal.md)。 如需在虛擬 WAN 中建立 VPN 閘道的步驟，請參閱 [使用 Azure 虛擬 Wan 建立站對站](virtual-wan-site-to-site-portal.md)連線。

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. 建立內部部署網路的網站

網站資源與虛擬 WAN 的非 ExpressRoute VPN 網站相同。 內部部署 VPN 裝置的 IP 位址現在可以是私人 IP 位址，或內部部署網路中的公用 IP 位址，可透過步驟1中建立的 ExpressRoute 私人對等互連來連線。

> [!NOTE]
> 內部部署 VPN 裝置的 IP 位址 *必須* 是透過 Azure ExpressRoute 私人對等互連向虛擬 WAN 中樞公告的位址首碼的一部分。
>

1. 移至瀏覽器中的 Azure 入口網站。 
1. 選取您所建立的中樞。 在 [虛擬 WAN 中樞] 頁面的 [連線 **能力**] 底下，選取 [ **VPN 網站**]。
1. 在 [ **VPN 網站** ] 頁面上，選取 [ **+ 建立網站**]。
1. 在 [建立網站]**** 頁面上，填寫下列欄位：
   * **訂**用帳戶：驗證訂用帳戶。
   * **資源群組**：選取或建立您想要使用的資源群組。
   * **區域**：輸入 VPN 網站資源的 Azure 區域。
   * **名稱**：輸入您想要用來參考內部部署網站的名稱。
   * **裝置廠商**：輸入內部部署 VPN 裝置的廠商。
   * **邊界閘道協定**：如果您的內部部署網路使用 BGP，請選取 [啟用]。
   * **私人位址空間**：輸入位於您內部部署網站上的 IP 位址空間。 目的地為此位址空間的流量會透過 VPN 閘道路由傳送至內部部署網路。
   * **中樞**：選取一或多個中樞來連接此 VPN 網站。 選取的中樞必須已建立 VPN 閘道。
1. 選取 [ **下一步： ** VPN 連結設定 >的連結：
   * **連結名稱**：您要用來參考此連接的名稱。
   * **提供者名稱**：此網站的網際網路服務提供者名稱。 針對 ExpressRoute 內部部署網路，它是 ExpressRoute 服務提供者的名稱。
   * **速度**：網際網路服務連結或 ExpressRoute 線路的速度。
   * **IP 位址**：位於內部部署網站上之 VPN 裝置的公用 IP 位址。 或者，針對 ExpressRoute 內部部署，這是透過 ExpressRoute 的 VPN 裝置的私人 IP 位址。

   如果已啟用 BGP，則會套用至在 Azure 中為此網站建立的所有連線。 在虛擬 WAN 上設定 BGP 相當於在 Azure VPN 閘道上設定 BGP。 
   
   您的內部部署 BGP 對等位址 *不* 能與裝置的 vpn IP 位址或 vpn 網站的虛擬網路位址空間相同。 在 VPN 裝置上，請針對 BGP 對等互連 IP 使用不同的 IP 位址。 它可以是指派給裝置上的回送介面的位址。 不過，它 *不能* 是 APIPA (169.254。*x*。*x*) 位址。 在代表位置的對應局域網路閘道中指定此位址。 如需 BGP 必要條件，請參閱[關於 BGP 與 Azure VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md)。

1. 選取 **[下一步：檢查 + 建立] >** 檢查設定值，並建立 VPN 網站。 如果您選取要連線的 **中樞** ，則會在內部部署網路與中樞 VPN 閘道之間建立連線。

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. 將 VPN 連線設定更新為使用 ExpressRoute

在您建立 VPN 網站並聯機到中樞之後，請使用下列步驟來設定連線以使用 ExpressRoute 私用對等互連：

1. 返回至虛擬 WAN 資源頁面，然後選取中樞資源。 或從 VPN 網站流覽至連線的中樞。

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="透過 ExpressRoute 的 VPN":::
1. 在 [連線 **能力**] 底下，選取 [ **VPN (站對站) **。

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="透過 ExpressRoute 的 VPN":::
1. 選取 VPN 網站上的省略號 (**...**) ，然後選取 [ **編輯此中樞的 vpn**連線]。

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="透過 ExpressRoute 的 VPN":::
1. 針對 [ **使用 Azure 私人 IP 位址**]，選取 **[是]**。 此設定會將中樞 VPN 閘道設定為在此連線的閘道上的中樞位址範圍內使用私人 IP 位址，而不是使用公用 IP 位址。 這可確保來自內部部署網路的流量會流經 ExpressRoute 私用對等互連路徑，而不是使用公用網際網路來進行此 VPN 連接。 下列螢幕擷取畫面顯示設定：

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="透過 ExpressRoute 的 VPN" border="false":::
1. 選取 [儲存]****。

儲存變更之後，中樞 VPN 閘道將會使用 VPN 閘道上的私人 IP 位址，透過 ExpressRoute 與內部部署 VPN 裝置建立 IPsec/IKE 連線。

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. 取得中樞 VPN 閘道的私人 IP 位址

下載 VPN 裝置設定，以取得中樞 VPN 閘道的私人 IP 位址。 您需要這些位址來設定內部部署 VPN 裝置。

1. 在您的中樞頁面上，選取 [連線 **] 下的**[ **VPN (站對站) ** 。
1. 在 [ **總覽** ] 頁面的頂端，選取 [ **下載 VPN**設定]。 

   Azure 會在資源群組的「microsoft-網路-[位置]」中建立儲存體帳戶，其中 *位置* 是 WAN 的位置。 將設定套用至您的 VPN 裝置之後，您可以刪除此儲存體帳戶。
1. 建立檔案之後，請選取要下載的連結。
1. 將組態套用至您的 VPN 裝置。

### <a name="vpn-device-configuration-file"></a>VPN 裝置設定檔案

裝置設定檔案包含設定內部部署 VPN 裝置時所要使用的設定。 當您檢視此檔案時，請注意下列資訊：

* **vpnSiteConfiguration**：此區段表示設定為連線至虛擬 WAN 之網站的裝置詳細資料。 它包含分支裝置的名稱和公用 IP 位址。
* **vpnSiteConnections**：本節提供下列設定的相關資訊：

    * 虛擬中樞虛擬網路的位址空間。<br/>範例：
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 連線至中樞之虛擬網路的位址空間。<br>範例：
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 虛擬中樞 VPN 閘道的 IP 位址。 因為 VPN 閘道的每個連線是由主動-主動設定中的兩個通道所組成，所以您會看到此檔案中列出這兩個 IP 位址。 在此範例中，您會看到 `Instance0` `Instance1` 每個網站的和，而它們是私人 ip 位址，而不是公用 ip 位址。<br>範例：
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * VPN 閘道連線的設定詳細資料，例如 BGP 和預先共用金鑰。 系統會自動為您產生預先共用金鑰。 您隨時都可以在 [ **總覽** ] 頁面上編輯自訂預先共用金鑰的連接。
  
### <a name="example-device-configuration-file"></a>範例裝置設定檔

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>設定 VPN 裝置

如果需要設定裝置的指示，您可以使用 [VPN 裝置組態指令碼頁面](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)中的指示，並留意下列注意事項：

* 未針對虛擬 WAN 寫入 VPN 裝置頁面上的指示。 但是，您可以使用設定檔案中的虛擬 WAN 值，手動設定 VPN 裝置。 
* 適用于 VPN 閘道的可下載裝置設定腳本不適用於虛擬 WAN，因為設定不同。
* 新的虛擬 WAN 可以支援 IKEv1 和 IKEv2。
* 虛擬 WAN 只能使用以路由為基礎的 VPN 裝置和裝置指示。

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. 查看您的虛擬 WAN

1. 移至虛擬 WAN。
1. 在 [概觀] 頁面中，地圖上的每個點都代表著中樞。
1. 在 [ **中樞與** 連線] 區段中，您可以看到中樞、網站、區域和 VPN 線上狀態。 您也可以查看傳入和傳出的位元組。

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. 監視連接

建立連線，以監視 Azure 虛擬機器 (VM) 和遠端網站之間的通訊。 如需有關如何設定連線監視的資訊，請參閱[監視網路通訊](~/articles/network-watcher/connection-monitor.md)。 來源欄位是 Azure 中的 VM IP，而目的地 IP 是網站 IP。

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. 清除資源

當您不再需要這些資源時，可以使用 [>new-azresourcegroup](/powershell/module/az.resources/remove-azresourcegroup) 移除資源群組及其包含的所有資源。 執行下列 PowerShell 命令，並 `myResourceGroup` 以您的資源組名取代：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

本文可協助您使用虛擬 WAN，透過 ExpressRoute 私用對等互連建立 VPN 連線。 若要深入瞭解虛擬 WAN 和相關功能，請參閱 [虛擬 wan 總覽](virtual-wan-about.md)。
