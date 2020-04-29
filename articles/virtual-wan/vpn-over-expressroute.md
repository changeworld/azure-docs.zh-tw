---
title: 設定 ExpressRoute 加密：適用于 Azure 虛擬 WAN 的 IPsec over ExpressRoute
description: 在本教學課程中，您將瞭解如何使用 Azure 虛擬 WAN，透過 ExpressRoute 私用對等互連來建立站對站 VPN 連線。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059293"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute 加密：透過 ExpressRoute 進行虛擬 WAN 的 IPsec

本文說明如何使用 Azure 虛擬 WAN，透過 Azure ExpressRoute 線路的私人對等互連，建立從內部部署網路到 Azure 的 IPsec/IKE VPN 連線。 這項技術可透過 ExpressRoute 提供內部部署網路與 Azure 虛擬網路之間的加密傳輸，而不需要經過公用網際網路或使用公用 IP 位址。

## <a name="topology-and-routing"></a>拓撲和路由

下圖顯示透過 ExpressRoute 私用對等互連的 VPN 連線範例：

![透過 ExpressRoute 的 VPN](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

此圖顯示透過 ExpressRoute 私人對等互連連線到 Azure 中樞 VPN 閘道的內部部署網路內的網路。 連線性的建立非常簡單：

1. 使用 ExpressRoute 線路和私用對等互連建立 ExpressRoute 連線能力。
2. 如本文所述，建立 VPN 連線能力。

此設定的重要層面是透過 ExpressRoute 和 VPN 路徑，在內部部署網路與 Azure 之間路由傳送。

### <a name="traffic-from-on-premises-networks-to-azure"></a>從內部部署網路到 Azure 的流量

對於從內部部署網路到 Azure 的流量，會透過 ExpressRoute 私用對等 BGP 和 VPN BGP 來公告 Azure 首碼（包括虛擬中樞和連線到中樞的所有輪輻虛擬網路）。 這會導致兩個從內部部署網路到 Azure 的網路路由（路徑）：

- 一個在受 IPsec 保護的路徑上
- 一個直接透過 ExpressRoute*而不*搭配 IPsec 保護 

若要將加密套用至通訊，您必須確定在圖表中，VPN 連線網路上的 Azure 路由是透過直接 ExpressRoute 路徑來進行，因此偏好透過內部部署 VPN 閘道。

### <a name="traffic-from-azure-to-on-premises-networks"></a>從 Azure 到內部部署網路的流量

相同的需求適用于從 Azure 到內部部署網路的流量。 若要確保在直接 ExpressRoute 路徑（不含 IPsec）上偏好使用 IPsec 路徑，您有兩個選項：

- 針對 VPN 連線的網路，在 VPN BGP 會話上公告更具體的首碼。 您可以透過 ExpressRoute 私用對等互連來公告包含 VPN 連線網路的較大範圍，然後在 VPN BGP 會話中提供更具體的範圍。 例如，透過 ExpressRoute 公告 10.0.0.0/16 和透過 VPN 的 10.0.1.0/24。

- 公告 VPN 和 ExpressRoute 的斷續首碼。 如果 VPN 連線的網路範圍與其他 ExpressRoute 連線網路不相交，您可以分別在 VPN 和 ExpressRoute BGP 會話中通告首碼。 例如，透過 ExpressRoute 公告 10.0.0.0/24 和透過 VPN 的 10.0.1.0/24。

在這兩個範例中，Azure 會透過 VPN 連線將流量傳送至 10.0.1.0/24，而不是直接透過 ExpressRoute 而不是 VPN 保護。

> [!WARNING]
> 如果您在 ExpressRoute 和 VPN 連線上公告*相同*的首碼，Azure 將直接使用 expressroute 路徑，而不需要 VPN 保護。
>

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. 使用閘道建立虛擬 WAN 和中樞

您必須先準備好下列 Azure 資源和對應的內部部署設定，才能繼續進行：

- Azure 虛擬 WAN
- 具有[ExpressRoute 網](virtual-wan-expressroute-portal.md)關和[VPN 閘道](virtual-wan-site-to-site-portal.md)的虛擬 WAN 中樞

如需建立 Azure 虛擬 WAN 和具有 ExpressRoute 關聯之中樞的步驟，請參閱[使用 Azure 虛擬 Wan 建立 ExpressRoute 關聯](virtual-wan-expressroute-portal.md)。 如需在虛擬 WAN 中建立 VPN 閘道的步驟，請參閱[使用 Azure 虛擬 Wan 建立站對站](virtual-wan-site-to-site-portal.md)連線。

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. 建立內部部署網路的網站

網站資源與虛擬 WAN 的非 ExpressRoute VPN 網站相同。 內部部署 VPN 裝置的 IP 位址現在可以是私人 IP 位址，或是內部部署網路中的公用 IP 位址，可透過在步驟1中建立的 ExpressRoute 私用對等互連來存取。

> [!NOTE]
> 內部部署 VPN 裝置的 IP 位址*必須*是透過 Azure ExpressRoute 私用對等互連向虛擬 WAN 中樞通告的位址首碼的一部分。
>

1. 在瀏覽器中移至 [Azure 入口網站]。 
1. 選取您建立的 WAN。 在 [WAN] 頁面的 [連線**能力**] 底下，選取 [ **VPN 網站**]。
1. 在 [ **VPN 網站**] 頁面上，選取 [ **+ 建立網站**]。
1. 在 [建立網站]**** 頁面上，填寫下列欄位：
   * **訂**用帳戶：確認訂用帳戶。
   * **資源群組**：選取或建立您想要使用的資源群組。
   * **區域**：輸入 VPN 網站資源的 Azure 區域。
   * **名稱**：輸入您想要用來參考內部部署網站的名稱。
   * **裝置廠商**：輸入內部部署 VPN 裝置的廠商。
   * **邊界閘道協定**：如果您的內部部署網路使用 BGP，請選取 [啟用]。
   * **私人位址空間**：輸入位於內部部署網站上的 IP 位址空間。 目的地為此位址空間的流量會透過 VPN 閘道路由傳送至內部部署網路。
   * **中樞**：選取一或多個中樞來連接此 VPN 網站。 選取的中樞必須已經建立 VPN 閘道。
1. 選取 [**下一步]：** VPN 連結設定 >的連結：
   * **連結名稱**：您要用來參考此連接的名稱。
   * **提供者名稱**：此網站的網際網路服務提供者名稱。 若為 ExpressRoute 內部部署網路，則為 ExpressRoute 服務提供者的名稱。
   * **速度**：網際網路服務連結或 ExpressRoute 線路的速度。
   * **IP 位址**：位於內部部署網站上的 VPN 裝置的公用 IP 位址。 或者，針對 ExpressRoute 內部部署，這是 VPN 裝置透過 ExpressRoute 的私人 IP 位址。

   如果已啟用 BGP，它會套用到在 Azure 中為此網站建立的所有連線。 在虛擬 WAN 上設定 BGP 相當於在 Azure VPN 閘道上設定 BGP。 
   
   您的內部部署 BGP 對等互連位址*不得*與 vpn 網站的裝置或虛擬網路位址空間 VPN 的 IP 位址相同。 在 VPN 裝置上，請針對 BGP 對等互連 IP 使用不同的 IP 位址。 它可以是指派給裝置上的回送介面的位址。 不過，它*不能*是 APIPA （169.254。*x*。*x*）位址。 請在代表位置的對應局域網路閘道上指定此位址。 如需 BGP 必要條件，請參閱[關於 BGP 與 Azure VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md)。

1. 選取 **[下一步]： [流覽 + 建立 >]** 以檢查設定值，並建立 VPN 網站。 如果您選取 [**中樞**] 來連線，則會在內部部署網路與中樞 VPN 閘道之間建立連線。

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. 將 VPN 連線設定更新為使用 ExpressRoute

建立 VPN 網站並聯機到中樞之後，請使用下列步驟來設定連線以使用 ExpressRoute 私用對等互連：

1. 返回 [虛擬 WAN 資源] 頁面，然後選取中樞資源。 或從 VPN 網站流覽至已連線的中樞。
1. 在 [連線**能力**] 底下，選取 **[VPN （站對站）**]。
1. 選取 VPN 網站上透過 ExpressRoute 的省略號（**...**），然後選取 [**編輯此中樞的 VPN**連線]。
1. 針對 [**使用 Azure 私人 IP 位址**]，選取 **[是]**。 設定會將中樞 VPN 閘道設為在此連線的閘道上，使用中樞位址範圍內的私人 IP 位址，而不是公用 IP 位址。 這可確保來自內部部署網路的流量會流經 ExpressRoute 私人對等互連路徑，而不是針對此 VPN 連線使用公用網際網路。 下列螢幕擷取畫面顯示設定。

   ![針對 VPN 連線使用私人 IP 位址的設定](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. 選取 [儲存]  。

儲存變更之後，中樞 VPN 閘道會使用 VPN 閘道上的私人 IP 位址，透過 ExpressRoute 建立與內部部署 VPN 裝置的 IPsec/IKE 連線。

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. 取得中樞 VPN 閘道的私人 IP 位址

下載 VPN 裝置設定，以取得中樞 VPN 閘道的私人 IP 位址。 您需要這些位址來設定內部部署 VPN 裝置。

1. 在您中樞的頁面上，選取 [連線] 底下的 **[VPN （站對站）** **]。**
1. 在 [**總覽**] 頁面的頂端，選取 [**下載 VPN**設定]。 

   Azure 會在資源群組 "microsoft-network-[location]" 中建立儲存體帳戶，其中*location*是 WAN 的位置。 將設定套用至您的 VPN 裝置之後，您就可以刪除此儲存體帳戶。
1. 建立檔案之後，請選取連結以下載。
1. 將組態套用至您的 VPN 裝置。

### <a name="vpn-device-configuration-file"></a>VPN 裝置設定檔

裝置設定檔包含設定內部部署 VPN 裝置時要使用的設定。 當您檢視此檔案時，請注意下列資訊：

* **vpnSiteConfiguration**：此區段表示設定為連線至虛擬 WAN 之網站的裝置詳細資料。 其中包含分支裝置的名稱和公用 IP 位址。
* **vpnSiteConnections**：本節提供下列設定的相關資訊：

    * 虛擬中樞虛擬網路的位址空間。<br/>範例：
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 連線至中樞之虛擬網路的位址空間。<br>範例：
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 虛擬中樞 VPN 閘道的 IP 位址。 因為 VPN 閘道的每個連線都是由主動-主動設定中的兩個通道所組成，所以您會看到這兩個 IP 位址都列在此檔案中。 在此範例中，您`Instance0`會`Instance1`看到每個網站的和，而且它們是私人 ip 位址，而不是公用 ip 位址。<br>範例：
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * VPN 閘道連線的設定詳細資料，例如 BGP 和預先共用金鑰。 系統會自動為您產生預先共用金鑰。 您隨時可以在 [**總覽**] 頁面上編輯自訂預先共用金鑰的連接。
  
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

* [VPN 裝置] 頁面上的指示不會針對虛擬 WAN 寫入。 但是，您可以使用設定檔中的虛擬 WAN 值，手動設定您的 VPN 裝置。 
* 適用于 VPN 閘道的可下載裝置設定腳本不適用於虛擬 WAN，因為設定不同。
* 新的虛擬 WAN 可以同時支援 IKEv1 和 IKEv2。
* 虛擬 WAN 只能使用以路由為基礎的 VPN 裝置和裝置指示。

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. 查看您的虛擬 WAN

1. 移至虛擬 WAN。
1. 在 [**總覽**] 頁面上，地圖上的每個點代表一個中樞。
1. 在 [**中樞和**連線] 區段中，您可以查看 [中樞]、[網站]、[區域] 和 [VPN 連線] 狀態。 您也可以查看傳入和傳出的位元組。

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. 監視連接

建立連線以監視 Azure 虛擬機器（VM）與遠端網站之間的通訊。 如需有關如何設定連線監視的資訊，請參閱[監視網路通訊](~/articles/network-watcher/connection-monitor.md)。 [來源] 欄位是 Azure 中的 VM IP，而 [目的地 IP] 是 [網站 IP]。

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. 清除資源

當您不再需要這些資源時，可以使用[remove-azresourcegroup](/powershell/module/az.resources/remove-azresourcegroup)來移除資源群組及其包含的所有資源。 執行下列 PowerShell 命令，並將取代`myResourceGroup`為您的資源組名：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

本文可協助您使用虛擬 WAN，透過 ExpressRoute 私人對等互連建立 VPN 連線。 若要深入瞭解虛擬 WAN 和相關功能，請參閱[虛擬 wan 總覽](virtual-wan-about.md)。
