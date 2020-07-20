---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ac23912b12bc6c7fcd40d7433cb4e2d257eeb5d0
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563019"
---
1. 在虛擬 WAN 的入口網站頁面的 [連線] 區段中，選取 [VPN 網站]，以開啟 [VPN 網站] 頁面。
2. 在 [VPN 網站] 頁面上，按一下 [+ 建立網站]。

   ![基本概念](./media/virtual-wan-tutorial-site-include/basics.png "基本概念")
3. 在 [建立 VPN 網站] 頁面的 [基本] 索引標籤中，完成以下欄位：

    * **區域** - 先前稱為位置。 這是您要在其中建立此網站資源的位置。
    * **名稱** - 您要用來參考內部部署網站的名稱。
    * **裝置廠商** - VPN 裝置廠商的名稱 (例如：Citrix、Cisco、Barracuda)。 這樣做可協助 Azure 小組深入了解您的環境，以便在未來增加額外最佳化的可能性，或協助您進行疑難排解。
    * **邊界閘道協定** - 啟用此選項表示來自該網站的所有連線將啟用 BGP。 最後，您會在 [連結] 區段中，為 VPN 網站的每個連結設定 BGP 資訊。 在虛擬 WAN 上設定 BGP 就等同於在 Azure 虛擬網路閘道 VPN 上設定 BGP。 內部部署 BGP 同儕節點位址不得與 VPN 到裝置的公用 IP 位址或 VPN 網站的 VNet 位址空間相同。 在 VPN 裝置上，請針對 BGP 對等互連 IP 使用不同的 IP 位址。 它可以是指派給裝置上的回送介面的位址。 但是，不能是 APIPA (169.254.x.x) 位址。 在代表位置的對應 VPN 網站中指定這個位址。 如需 BGP 必要條件，請參閱[關於 BGP 與 Azure VPN 閘道](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)。 一旦已啟用 VPN 網站 BGP 設定，您可以隨時編輯 VPN 連線，以更新其 BGP 參數 (連結上的同儕節點 IP 與 AS #)。
    * **私人位址空間** - 位於內部部署網站上的 IP 位址空間。 流向此位址空間的流量會路由至您本機網站。 當網站未啟用 BGP 時，這是必要項目。
    * **中樞** - 要讓網站連線到的中樞。 網站只能連線到具有 VPN 閘道的中樞。 如果您沒有看到中樞，請先在該中樞內建立 VPN 閘道。
4. 請選取 [連結]，以新增分支上實體連結的相關資訊。 如果您有虛擬 WAN 夥伴 CPE 裝置，請查看這些裝置是否與 Azure 交換該資訊，作為從其系統設定之分支資訊上傳的一部分。

   ![連結](./media/virtual-wan-tutorial-site-include/links.png "連結")

    * **連結名稱** - 您想要為 VPN 網站實體連結提供的名稱。 範例：mylink1。
    * **提供者名稱** - VPN 網站的實體連結名稱。 範例：ATT、Verizon。
    * **速度** - 這是 VPN 裝置在分支位置的速度。 範例：50，代表 VPN 裝置在分支網站的速度為 50 Mbps。
    * **IP 位址/FQDN** - 使用此連結之內部部署裝置的公用 IP 位址。 或者，您可以為 ExpressRoute 後方的內部部署 VPN 裝置，提供私人 IP 位址。 您也可入完整的網域名稱。 例如 something.contoso.com。 FQDN 應該可以從 VPN 閘道解析。 如果裝載此 FQDN 的 DNS 伺服器可透過網際網路連線，就可能發生這種情況。 當同時指定 IP 位址和 FQDN 時，IP 位址的優先順序會較高。

      >[!NOTE]
      >* 支援每個 FQDN 有一個 IPv4 位址。 如果將 FQDN 解析為多個 IP 位址，則 VPN 閘道會從清單中挑選第一個 IP4 位址。 目前不支援 IPv6 位址。
      >* VPN 閘道會以每 5 分鐘重新整理一次的方式維護 DNS 快取。 閘道只會嘗試解析已中斷連線通道的 FQDN。 閘道重設或設定變更也會觸發 FQDN 解析。
      >
5. 您可以使用此核取方塊以刪除或新增其他連結。 每個 VPN 網站支援四個連結。 例如，如果在分支位置有四個 ISP (網際網路服務提供者)，您可以建立四個連結。 每個 ISP 一個連結，並為每個連結提供資訊。
6. 填寫完欄位之後，請選取 [檢閱 + 建立]，以確認並建立網站。
7. 檢視 [VPN 網站] 頁面上的狀態。 網站將移至 [需要的連線]，因為網站尚未連線到中樞。