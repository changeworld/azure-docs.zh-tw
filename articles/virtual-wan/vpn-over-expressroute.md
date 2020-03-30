---
title: 配置快速路由加密：通過 Azure 虛擬 WAN 的快速路由進行 IPsec
description: 在本教程中，瞭解如何使用 Azure 虛擬 WAN 通過 ExpressRoute 專用對等互連創建網站到網站 VPN 連接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059293"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>快速路由加密：通過虛擬 WAN 的快速路由進行 IPsec

本文介紹如何使用 Azure 虛擬 WAN 通過 Azure ExpressRoute 電路的專用對等互連從本地網路建立 IPsec/IKE VPN 連接。 此技術可以通過 ExpressRoute 在本地網路和 Azure 虛擬網路之間提供加密傳輸，而無需通過公共 Internet 或使用公共 IP 位址。

## <a name="topology-and-routing"></a>拓撲和路由

下圖顯示了通過 ExpressRoute 專用對等互連的 VPN 連接示例：

![VPN 通過快速路由](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

該圖顯示了通過 ExpressRoute 專用對等互連連接到 Azure 中心 VPN 閘道的本地網路中的網路。 連接建立非常簡單：

1. 通過快速路由電路和專用對等互連建立快速路由連接。
2. 建立本文中所述的 VPN 連接。

此配置的一個重要方面是本地網路和 Azure 之間路由到 ExpressRoute 和 VPN 路徑。

### <a name="traffic-from-on-premises-networks-to-azure"></a>從本地網路到 Azure 的流量

對於從本地網路到 Azure 的流量，Azure 首碼（包括虛擬集線器和連接到集線器的所有分支虛擬網路）通過 ExpressRoute 專用對等 BGP 和 VPN BGP 進行通告。 這將導致從本地網路向 Azure 路由（路徑）兩個：

- 一個在 IPsec 保護的路徑上
- 直接通過快速路由，*無需*IPsec 保護 

要對通信應用加密，必須確保對於關係圖中的 VPN 連接網路，首選通過本地 VPN 閘道的 Azure 路由，而不是直接 ExpressRoute 路徑。

### <a name="traffic-from-azure-to-on-premises-networks"></a>從 Azure 到本地網路的流量

相同的要求也適用于從 Azure 到本地網路的流量。 為了確保 IPsec 路徑優先于直接 ExpressRoute 路徑（不含 IPsec），您有兩個選項：

- 在 VPN 連接的網路的 VPN BGP 會話上通告更具體的首碼。 您可以通過 ExpressRoute 專用對等互連通告包含 VPN 連接網路的較大範圍，然後在 VPN BGP 會話中發佈更具體的範圍。 例如，通過 ExpressRoute 通告 10.0.0.0/16，通過 VPN 通告 10.0.1.0/24。

- 通告 VPN 和 ExpressRoute 的不相交首碼。 如果 VPN 連接的網路範圍與其他 ExpressRoute 連接的網路不相交，則可以分別在 VPN 和 ExpressRoute BGP 會話中通告首碼。 例如，通過 ExpressRoute 通告 10.0.0.0/24，在 VPN 上通告 10.0.1.0/24。

在這兩個示例中，Azure 將通過 VPN 連接將流量發送到 10.0.1.0/24，而不是直接通過沒有 VPN 保護的 ExpressRoute 發送流量。

> [!WARNING]
> 如果在 ExpressRoute 和 VPN 連接上通告*相同的*首碼，Azure 將直接使用 ExpressRoute 路徑，而無需 VPN 保護。
>

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. 創建具有閘道的虛擬 WAN 和集線器

在繼續操作之前，必須部署以下 Azure 資源和相應的本地配置：

- Azure 虛擬 WAN
- 具有[快速路由閘道](virtual-wan-expressroute-portal.md)和[VPN 閘道](virtual-wan-site-to-site-portal.md)的虛擬廣域網路中心

有關創建 Azure 虛擬 WAN 和具有快速路由關聯的集線器的步驟，請參閱[使用 Azure 虛擬 WAN 創建快速路由關聯](virtual-wan-expressroute-portal.md)。 有關在虛擬 WAN 中創建 VPN 閘道的步驟，請參閱[使用 Azure 虛擬 WAN 創建網站到網站的連接](virtual-wan-site-to-site-portal.md)。

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. 為本地網路創建網站

網站資源與虛擬 WAN 的非 ExpressRoute VPN 網站相同。 本地 VPN 設備的 IP 位址現在可以是私人 IP 位址，也可以是本地網路中的公共 IP 位址，可通過步驟 1 中創建的 ExpressRoute 專用對等互連進行。

> [!NOTE]
> 本地 VPN 設備的 IP 位址*必須是*通過 Azure ExpressRoute 專用對等互連通告給虛擬 WAN 中心的位址首碼的一部分。
>

1. 轉到瀏覽器中的 Azure 門戶。 
1. 選擇您創建的 WAN。 在 WAN 頁面上，在 **"連接**"下，選擇**VPN 網站**。
1. 在**VPN 網站**頁面上，選擇 **"創建網站**"。
1. 在 [建立網站]**** 頁面上，填寫下列欄位：
   * **訂閱**：驗證訂閱。
   * **資源組**：選擇或創建要使用的資源組。
   * **區域**：輸入 VPN 網站資源的 Azure 區域。
   * **名稱**：輸入要引用本地網站的名稱。
   * **設備供應商**：輸入本地 VPN 設備的供應商。
   * **邊界閘道協定**：如果您的本地網路使用 BGP，請選擇"啟用"。
   * **專用位址空間**：輸入位於本地網站上的 IP 位址空間。 發送到此位址空間的流量通過 VPN 閘道路由到本地網路。
   * **集線器**：選擇一個或多個集線器以連接此 VPN 網站。 所選中心必須已創建 VPN 閘道。
1. 選擇 **"下一步"：VPN 連結設置的連結>：**
   * **連結名稱**：要引用此連接的名稱。
   * **供應商名稱**：此網站的互聯網服務提供者的名稱。 對於 ExpressRoute 本地網路，它是 ExpressRoute 服務提供者的名稱。
   * **速度**：互聯網服務鏈路或快速路由電路的速度。
   * **IP 位址**：駐留在本地網站的 VPN 設備的公共 IP 位址。 或者，對於本地的 ExpressRoute，它是通過 ExpressRoute 的 VPN 設備的私人 IP 位址。

   如果啟用了 BGP，它將應用於 Azure 中為此網站創建的所有連接。 在虛擬 WAN 上配置 BGP 等效于在 Azure VPN 閘道上配置 BGP。 
   
   本地 BGP 對等位址*不得*與 VPN 到設備的 IP 位址或 VPN 網站的虛擬網路位址空間相同。 在 VPN 裝置上，請針對 BGP 對等互連 IP 使用不同的 IP 位址。 它可以是指派給裝置上的回送介面的位址。 但是 *，它不可能是*APIPA （169.254）。*x*. .*x*） 位址。 在表示位置的相應本地網路閘道中指定此位址。 如需 BGP 必要條件，請參閱[關於 BGP 與 Azure VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md)。

1. 選擇 **"下一步"：查看 + 創建>** 以檢查設置值並創建 VPN 網站。 如果選擇要連接**的集線器**，則將在本地網路和中心 VPN 閘道之間建立連接。

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. 更新 VPN 連接設置以使用 ExpressRoute

創建 VPN 網站並連接到集線器後，請使用以下步驟將連接配置為使用 ExpressRoute 專用對等互連：

1. 返回虛擬 WAN 資源頁，然後選擇中心資源。 或者從 VPN 網站導覽到連接的集線器。
1. 在**連接**下，選擇**VPN（網站到網站）。**
1. 通過 ExpressRoute 在 VPN 網站上選擇省略號 （**...），** 然後選擇 **"編輯 VPN 連接到此中心**"。
1. 對於**使用 Azure 私人 IP 位址**，請選擇"**是**"。 該設置將集線器 VPN 閘道配置為使用此連接閘道上的集線器位址範圍內的私人 IP 位址，而不是公共 IP 位址。 這將確保來自本地網路的流量遍歷 ExpressRoute 專用對等路徑，而不是使用此 VPN 連接使用公共 Internet。 以下螢幕截圖顯示了設置。

   ![用於為 VPN 連接使用私人 IP 位址的設置](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. 選取 [儲存]****。

保存更改後，集線器 VPN 閘道將使用 VPN 閘道上的私人 IP 位址通過 ExpressRoute 與本地 VPN 設備建立 IPsec/IKE 連接。

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. 獲取集線器 VPN 閘道的私人 IP 位址

下載 VPN 設備配置以獲取集線器 VPN 閘道的私人 IP 位址。 您需要這些位址來配置本地 VPN 設備。

1. 在集線器的頁面上，選擇**連接**下的**VPN（網站到網站）。**
1. 在 **"概述"** 頁的頂部，選擇 **"下載 VPN 配置**"。 

   Azure 在資源組中創建一個存儲帳戶"Microsoft-網路-位置"，*其中位置*是 WAN 的位置。 將配置應用於 VPN 設備後，可以刪除此存儲帳戶。
1. 創建檔後，選擇要下載該檔的連結。
1. 將組態套用至您的 VPN 裝置。

### <a name="vpn-device-configuration-file"></a>VPN設備設定檔

設備設定檔包含配置本地 VPN 設備時要使用的設置。 當您檢視此檔案時，請注意下列資訊：

* **vpnSite配置**：此部分表示作為連接到虛擬 WAN 的網站設置的設備詳細資訊。 它包括分支設備的名稱和公共 IP 位址。
* **vpnSite連接**：本節提供有關以下設置的資訊：

    * 虛擬中心虛擬網路的位址空間。<br/>範例：
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 位址連接到集線器的虛擬網路的空間。<br>範例：
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 虛擬中心 VPN 閘道的 IP 位址。 由於 VPN 閘道的每個連接由兩個處於主動-主動配置中的隧道組成，因此您將看到此檔中列出的兩個 IP 位址。 在此示例中，您可以看到`Instance0`每個網站，`Instance1`它們是私人 IP 位址，而不是公共 IP 位址。<br>範例：
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * VPN 閘道連接的配置詳細資訊，如 BGP 和預共用金鑰。 自動為您生成預共用金鑰。 您可以隨時在 **"概述"** 頁上編輯自訂預共用金鑰的連接。
  
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

* VPN 設備頁面上的說明不是為虛擬 WAN 編寫的。 但是，您可以使用設定檔中的虛擬 WAN 值手動設定 VPN 設備。 
* 用於 VPN 閘道的可下載設備配置腳本不適合虛擬 WAN，因為配置不同。
* 新的虛擬廣域網路可以同時支援 IKEv1 和 IKEv2。
* 虛擬 WAN 只能使用基於路由的 VPN 設備和設備指令。

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. 查看虛擬廣域網路

1. 轉到虛擬廣域網路。
1. 在 [概觀]**** 頁面中，地圖上的每個點都代表著中樞。
1. 在 **"集線器和連接**"部分中，您可以查看中心、網站、區域和 VPN 連接狀態。 您還可以查看位元組進出。

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. 監控連接

創建連接以監視 Azure 虛擬機器 （VM） 和遠端站台之間的通信。 如需有關如何設定連線監視的資訊，請參閱[監視網路通訊](~/articles/network-watcher/connection-monitor.md)。 源欄位是 Azure 中的 VM IP，目標 IP 是網站 IP。

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. 清理資源

當您不再需要這些資源時，可以使用[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)刪除資源組及其包含的所有資源。 運行以下 PowerShell 命令，然後`myResourceGroup`替換為資源組的名稱：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

本文可説明您使用虛擬 WAN 通過 ExpressRoute 專用對等互連創建 VPN 連接。 要瞭解有關虛擬 WAN 和相關功能的詳細資訊，請參閱[虛擬 WAN 概述](virtual-wan-about.md)。
