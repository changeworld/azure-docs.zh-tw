---
title: 在 Azure VMware 解決方案中建立 IPSec 通道
description: 瞭解如何建立虛擬 WAN 中樞，以在 Azure VMware 解決方案中建立 IPSec 通道。
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 63318b9fdd0de5e0ce102fafe332f40f595f38f1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357839"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>在 Azure VMware 解決方案中建立 IPSec 通道

在本文中，我們將逐步解說如何在 Microsoft Azure Virtual WAN hub 中終止 (IPsec IKEv1 和 IKEv2) 站對站通道的建立 VPN。 我們會建立 Azure 虛擬 WAN 中樞和 VPN 閘道，並附加公用 IP 位址。 接著，我們將建立 Azure ExpressRoute 閘道並建立 Azure VMware 解決方案端點。 我們也會詳細說明如何啟用以原則為基礎的 VPN 內部部署設定。 

## <a name="topology"></a>拓撲

![VPN 站對站通道架構。](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Azure 虛擬中樞包含 Azure VMware 解決方案 ExpressRoute 閘道和站對站 VPN 閘道。 它會將內部部署 VPN 裝置連接到 Azure VMware 解決方案端點。

## <a name="before-you-begin"></a>開始之前

若要建立站對站 VPN 通道，您必須建立在內部部署 VPN 裝置上終止的公開 IP 位址。

## <a name="create-a-virtual-wan-hub"></a>建立虛擬 WAN 中樞

1. 在 Azure 入口網站中，搜尋 **虛擬 wan** 。 選取 [+新增]  。 [建立 WAN] 頁面隨即開啟。  

2. 在 [ **建立 WAN** ] 頁面上輸入必要欄位，然後選取 [ **審核 + 建立** ]。
   
   | 欄位 | 值 |
   | --- | --- |
   | **訂用帳戶** | 值會預先填入屬於資源群組的訂用帳戶。 |
   | **資源群組** | 虛擬 WAN 是全域資源，不局限于特定區域。  |
   | **資源群組位置** | 若要建立虛擬 WAN 中樞，您必須設定資源群組的位置。  |
   | **名稱** |   |
   | **型別** | 選取 [ **標準** ]，而不只允許 VPN 閘道流量。  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="建立 WAN。":::

3. 在 [Azure 入口網站中，選取您在上一個步驟中建立的虛擬 WAN、選取 [ **建立虛擬中樞** ]、輸入必要欄位，然後選取 **[下一步：站對站]** 。 

   | 欄位 | 值 |
   | --- | --- |
   | **區域** | 從管理的觀點來看，需要選取區域。  |
   | **Name** |    |
   | **中樞私人位址空間** | 使用 `/24` (最小) 來輸入子網。  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="建立虛擬中樞。":::

4. 在 [ **網站對網站** ] 索引標籤上，從 [ **閘道縮放單位** ] 下拉式清單中設定匯總輸送量來定義站對站閘道。 

   >[!TIP]
   >一個縮放單位 = 500 Mbps。 縮放單位是成對的，每個都支援 500 Mbps。
  
5. 在 [ **expressroute** ] 索引標籤上，建立 expressroute 閘道。 

   >[!TIP]
   >縮放單位值為 2 Gbps。 

    大約需要30分鐘的時間來建立每個中樞。 

## <a name="create-a-vpn-site"></a>建立 VPN 網站 

1. 在 [Azure 入口網站的 **最新資源** 中，選取您在上一節中建立的虛擬 WAN。

2. 在虛擬中樞的 **總覽** 中，選取連線 **Connectivity**  >  **vpn (站對站)** ，然後選取 [ **建立新的 vpn 網站** ]。


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="建立 VPN 網站。":::  
 
3. 在 [ **基本** ] 索引標籤上，輸入必要的欄位，然後選取 **[下一步：連結]** 。 

   | 欄位 | 值 |
   | --- | --- |
   | **區域** | 您在上一節中指定的相同區域。  |
   | **Name** |  |
   | **裝置廠商** |  |
   | **邊界閘道協定** | 設定為 [ **啟用** ]，以確保 Azure VMware 解決方案和內部部署伺服器會在通道之間通告其路由。 如果停用，則必須以手動方式維護需要通告的子網。 如果缺少子網，HCX 將無法形成服務網格。 如需詳細資訊，請參閱  [關於 BGP 與 AZURE VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md)。 |
   | **私人位址空間**  | 輸入內部部署 CIDR 區塊。  它是用來將所有在內部部署中系結的流量路由傳送至整個通道。  只有當您未啟用 BGP 時，才需要 CIDR 區塊。 |
   | **連接至** |   |

4. 在 [ **連結** ] 索引標籤上，填寫必要的欄位，然後選取 [ **審核 + 建立** ]。 指定連結和提供者名稱，可讓您區分最後可能建立為中樞一部分的任何數目的閘道。 BGP 和自發系統編號 (ASN) 在您的組織內必須是唯一的。
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a> (選擇性) 定義以原則為基礎的 VPN 站對站通道的 VPN 網站

本節僅適用于以原則為基礎的 Vpn。 以原則為基礎的 (或靜態路由型) VPN，在大部分情況下都是由內部部署 VPN 裝置功能所驅動。 他們需要指定內部部署和 Azure VMware 解決方案網路。 針對具有 Azure 虛擬 WAN 中樞的 Azure VMware 解決方案，您無法選取 *任何* 網路。 相反地，您必須指定所有相關的內部部署和 Azure VMware 解決方案虛擬 WAN 中樞範圍。 這些中樞範圍用來指定原則基底 VPN 通道內部部署端點的加密網域。 Azure VMware 解決方案端只需要啟用以原則為基礎的流量選取器指示器。 

1. 在 Azure 入口網站中，移至您的虛擬 WAN 中樞網站;在 [連線 **能力** ] 底下，選取 [ **VPN (站對站)** 。

2. 選取您的 VPN 網站名稱，然後選取最右側的省略號 ( ... ) ;然後選取 [ **編輯此中樞的 VPN** 連線]。
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="編輯此中樞的 VPN 連接。" lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. 編輯 VPN 網站與中樞之間的連接，然後選取 [ **儲存** ]。
   - 網際網路通訊協定安全性 (IPSec) ，請選取 [ **自訂** ]。
   - 使用以原則為基礎的流量選取器，選取 [ **啟用** ]
   - 指定 **Ike 階段 1** 和 **ike 階段 2 (ipsec)** 的詳細資料。 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="編輯 VPN 連線"::: 
 
    您的流量選取器或屬於以原則為基礎之加密網域一部分的子網應該是：
    
    - 虛擬 WAN 中樞/24
    - Azure VMware 解決方案私用雲端/22
    - 已連線的 Azure 虛擬網路 (（如果有的話）) 

## <a name="connect-your-vpn-site-to-the-hub"></a>將您的 VPN 網站連線到中樞

1. 核取您的 VPN 網站名稱旁邊的方塊 (請參閱先前的 **Vpn 站對站** 螢幕擷取畫面) ，然後選取 **[連線 vpn 網站]** 。 在 [ **預先共用金鑰** ] 欄位中，輸入先前為內部部署端點定義的金鑰。 如果您沒有之前定義的金鑰，您可以將此欄位保留空白，系統會自動為您產生金鑰。 
 
    只有在您要部署中樞內的防火牆，並且是透過該通道連線的下一個躍點時，才啟用 [ **傳播預設路由** ]。

    選取 [連接]  。 [線上狀態] 畫面會顯示建立通道的狀態。

2. 前往虛擬 WAN 總覽。 開啟 [VPN 網站] 頁面，並下載 VPN 設定檔，將它套用至內部部署端點。  

3. 現在，我們會將 Azure VMware 解決方案 ExpressRoute 修補到虛擬 WAN 中樞。  (此步驟需要先建立私人雲端。 ) 

    移至 Azure VMware Solution 私用雲端的 [連線 **能力** ] 區段。 在 [ **ExpressRoute** ] 索引標籤上，選取 [ **+ 要求授權金鑰** ]。 為其命名，然後選取 [ **建立** ]。  (可能需要約30秒的時間來建立金鑰。 ) 複製 ExpressRoute 識別碼和授權金鑰。 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="複製 Express Route ID 和授權金鑰。":::

    > [!NOTE]
    > 授權金鑰會在一段時間後消失，因此請在出現後立即複製。

4. 接下來，我們會將 Azure VMware 解決方案和 VPN 閘道一起連結到虛擬 WAN 中樞。 在 Azure 入口網站中，開啟您稍早建立的虛擬 WAN。 選取已建立的虛擬 WAN 中樞，然後在左窗格中選取 [ **ExpressRoute** ]。 選取 [ **+ 兌換授權金鑰** ]。

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="兌換授權金鑰。":::

    將授權金鑰貼入 [授權金鑰] 欄位，然後將 ExpressRoute 識別碼貼到 [ **對等線路 URI** ] 欄位中。 請務必選取 **[自動將此 ExpressRoute 線路與中樞建立關聯]。** 選取 [ **新增** ] 以建立連結。 

5. 若要測試您的連線，請 [建立 NSX-T 區段](./tutorial-nsx-t-network-segment.md) ，並在網路上布建 VM。 藉由 ping 內部部署和 Azure VMware 解決方案端點來進行測試。
