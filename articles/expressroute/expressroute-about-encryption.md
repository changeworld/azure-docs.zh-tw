---
title: Azure ExpressRoute：關於加密
description: 深入瞭解 ExpressRoute 加密。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: cherylmc
ms.openlocfilehash: 77755ab6bdbb3c1e6416475f5066b5dd463eb7f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82838751"
---
# <a name="expressroute-encryption"></a>ExpressRoute 加密
 
ExpressRoute 支援幾種加密技術，可確保您的網路與 Microsoft 網路之間資料的機密性和完整性。

## <a name="point-to-point-encryption-by-macsec-faq"></a>依 MACsec 的點對點加密常見問題
MACsec 是[IEEE 標準](https://1.ieee802.org/security/802-1ae/)。 它會將媒體存取控制（MAC）層級或網路層2上的資料加密。 當您透過[ExpressRoute Direct](expressroute-erdirect-about.md)連接到 microsoft 時，您可以使用 MACsec 來加密您的網路裝置與 microsoft 網路裝置之間的實體連結。 根據預設，ExpressRoute Direct 埠上的 MACsec 已停用。 您會攜帶自己的 MACsec 金鑰進行加密，並將它儲存在[Azure Key Vault](../key-vault/general/overview.md)中。 您決定何時要旋轉金鑰。 請參閱下面的其他常見問題。
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>我可以在 ExpressRoute 提供者布建的 ExpressRoute 線路上啟用 MACsec 嗎？
否。 MACsec 會使用一個實體（也就是客戶）所擁有的金鑰，來加密實體連結上的所有流量。 因此，它僅適用于 ExpressRoute Direct。
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>我可以加密 ExpressRoute Direct 埠上的某些 ExpressRoute 線路，並在未加密的相同埠上保留其他線路嗎？ 
否。 MACsec 啟用後，所有網路控制流量（例如，BGP 資料流量和客戶資料流量）都會加密。 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>當我啟用/停用 MACsec 或更新 MACsec 金鑰時，我的內部部署網路會透過 ExpressRoute 失去與 Microsoft 的連線能力嗎？
是。 針對 MACsec 設定，我們只支援預先共用金鑰模式。 這表示您需要在您的裝置和 Microsoft （透過我們的 API）上更新金鑰。 這項變更不是不可部分完成的，因此當兩端之間有索引鍵不相符時，您將會失去連線能力。 我們強烈建議您針對設定變更排程維護時段。 若要將停機時間降到最低，建議您在將網路流量切換至另一個連結之後，一次更新一個 ExpressRoute Direct 連結的設定。  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>如果我的裝置與 Microsoft 之間的 MACsec 金鑰不相符，流量是否會繼續流動？
否。 如果 MACsec 已設定，而且發生金鑰不符的情況，您就會失去與 Microsoft 的連線能力。 換句話說，我們不會回復到未加密的連線，而是公開您的資料。 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>啟用 ExpressRoute Direct 的 MACsec 會降低網路效能嗎？
MACsec 加密和解密會發生在我們使用的路由器上的硬體中。 對我們的端不會有任何效能影響。 不過，您應該向網路廠商檢查您所使用的裝置，並查看 MACsec 是否有任何效能含意。
### <a name="which-cipher-suites-are-supported-for-encryption"></a>支援加密的加密套件有哪些？
我們只支援 AES128 和 AES256 的[擴充封包編號](https://1.ieee802.org/security/802-1aebw/)版本。 此外，請在您的裝置上停用 MACsec 設定中的[安全通道識別碼（SCI）](https://en.wikipedia.org/wiki/IEEE_802.1AE) 。 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>IPsec 常見問題的端對端加密
IPsec 是一種[IETF 標準](https://tools.ietf.org/html/rfc6071)。 它會以網際網路通訊協定（IP）層級或網路層3來加密資料。 您可以使用 IPsec 來加密內部部署網路與 Azure 上的虛擬網路（VNET）之間的端對端連線。 請參閱下面的其他常見問題。
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>除了 ExpressRoute Direct 埠上的 MACsec 之外，我還可以啟用 IPsec 嗎？
是。 MACsec 可保護您與 Microsoft 之間的實體連接。 IPsec 會保護您與您的虛擬網路在 Azure 上的端對端連線。 您可以獨立啟用它們。 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>我可以使用 Azure VPN 閘道來設定我的內部部署網路與 Azure 虛擬網路之間的 IPsec 通道嗎？
是。 您可以透過 ExpressRoute 線路的 Microsoft 對等互連來設定此 IPsec 通道。 遵循我們的設定[指南](site-to-site-vpn-over-microsoft-peering.md)。
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>我可以使用 Azure VPN 閘道，透過 Azure 私人對等互連來設定 IPsec 通道嗎？
如果您採用 Azure 虛擬 WAN，您可以遵循[下列步驟](../virtual-wan/vpn-over-expressroute.md)來加密端對端連線。 如果您有一般的 Azure VNET，您可以在 VNET 中部署協力廠商 VPN 閘道，並在其與您的內部部署 VPN 閘道之間建立 IPsec 通道。
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>在 ExpressRoute 連線上啟用 IPsec 之後，我會得到什麼輸送量？
如果使用 Azure VPN 閘道，請在[這裡檢查效能數位](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 如果使用協力廠商 VPN 閘道，請洽詢廠商以取得效能資料。

## <a name="next-steps"></a>後續步驟
如需 MACsec 設定的詳細資訊，請參閱[Configure MACsec](expressroute-howto-macsec.md) 。

如需 IPsec 設定的詳細資訊，請參閱[Configure ipsec](site-to-site-vpn-over-microsoft-peering.md) 。
