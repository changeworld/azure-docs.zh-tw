---
title: Azure ExpressRoute：關於加密
description: 深入瞭解 ExpressRoute 加密。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: duau
ms.openlocfilehash: 46f0a0e86c5db612f440bcf631329d2800251dab
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397792"
---
# <a name="expressroute-encryption"></a>ExpressRoute 加密
 
ExpressRoute 支援幾種加密技術，以確保您的網路與 Microsoft 網路之間的資料進行機密性和完整性。

## <a name="point-to-point-encryption-by-macsec-faq"></a>依 MACsec 的點對點加密常見問題
MACsec 是 [IEEE 標準](https://1.ieee802.org/security/802-1ae/)。 它會將媒體存取控制的資料加密 (MAC) 層級或網路層2。 當您透過 [ExpressRoute Direct](expressroute-erdirect-about.md)連接到 microsoft 時，可以使用 MACsec 來加密網路裝置與 microsoft 網路裝置之間的實體連結。 預設會停用 ExpressRoute Direct 埠上的 MACsec。 您可以攜帶自己的 MACsec 金鑰進行加密，並將它儲存在 [Azure Key Vault](../key-vault/general/overview.md)中。 您決定何時要輪替金鑰。 請參閱下列其他常見問題。
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>我可以在 ExpressRoute 提供者布建的 ExpressRoute 線路上啟用 MACsec 嗎？
不會。 MACsec 會使用一個實體所擁有的金鑰來加密實體連結上的所有流量， (即客戶) 。 因此，它只適用于 ExpressRoute Direct。
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>我是否可以加密 ExpressRoute Direct 埠上的一些 ExpressRoute 線路，並將其他線路加密在相同的埠上？ 
不會。 MACsec 啟用之後，所有網路控制流量（例如，BGP 資料流量和客戶資料流量）都會加密。 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>當我啟用/停用 MACsec 或更新 MACsec 金鑰時，我的內部部署網路是否會失去與 Microsoft 透過 ExpressRoute 的連線能力？
可以。 在 MACsec 設定中，我們只支援預先共用金鑰模式。 這表示您必須透過我們的 API) ，在您的裝置和 Microsoft 的 (上更新金鑰。 這項變更不是不可部分完成的，因此當兩端之間的金鑰不相符時，您將會失去連線能力。 強烈建議您排程設定變更的維護時段。 若要將停機時間降到最低，我們建議您在將網路流量切換到另一個連結之後，一次更新一個 ExpressRoute Direct 連結的設定。  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>如果我的裝置與 Microsoft 之間的 MACsec 金鑰不相符，流量是否會繼續流動？
不會。 如果設定了 MACsec，且發生金鑰不符的情況，您就會失去與 Microsoft 的連線能力。 換句話說，我們不會切換回未加密的連接，而會公開您的資料。 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>啟用 ExpressRoute Direct 上的 MACsec 會降低網路效能嗎？
MACsec 加密和解密是在我們使用的路由器上的硬體進行。 對我們而言，並不會對效能造成影響。 不過，您應該向網路廠商詢問您所使用的裝置，並查看 MACsec 是否有任何效能暗示。
### <a name="which-cipher-suites-are-supported-for-encryption"></a>加密支援哪些加密套件？
我們只支援 AES128 和 AES256 的 [擴充封包編號](https://1.ieee802.org/security/802-1aebw/) 版本。 此外，請停用裝置上的 MACsec 設定中 [ (科幻) 的安全通道識別碼 ](https://en.wikipedia.org/wiki/IEEE_802.1AE) 。 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>IPsec 的端對端加密常見問題
IPsec 是 [IETF 標準](https://tools.ietf.org/html/rfc6071)。 它會以網際網路通訊協定加密 (IP) 層級或網路層級3的資料。 您可以使用 IPsec 來加密內部部署網路與虛擬網路之間的端對端連線， (Azure 上的 VNET) 。 請參閱下列其他常見問題。
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>除了在 ExpressRoute Direct 埠上的 MACsec 之外，還可以啟用 IPsec 嗎？
可以。 MACsec 可保護您與 Microsoft 之間的實體連接。 IPsec 可保護您與 Azure 虛擬網路之間的端對端連線。 您可以獨立啟用它們。 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>我可以使用 Azure VPN 閘道來設定我的內部部署網路與 Azure 虛擬網路之間的 IPsec 通道嗎？
可以。 您可以透過 ExpressRoute 線路的 Microsoft 對等互連來設定此 IPsec 通道。 遵循我們的設定 [指南](site-to-site-vpn-over-microsoft-peering.md)。
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>我可以使用 Azure VPN 閘道來透過 Azure 私人對等互連來設定 IPsec 通道嗎？
如果您採用 Azure 虛擬 WAN，您可以遵循 [下列步驟](../virtual-wan/vpn-over-expressroute.md) 來加密端對端連線。 如果您有一般的 Azure VNET，您可以在 VNET 中部署協力廠商 VPN 閘道，並在其和內部部署 VPN 閘道之間建立 IPsec 通道。
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>在 ExpressRoute 連線上啟用 IPsec 之後，我將會得到什麼輸送量？
如果使用 Azure VPN 閘道，請在 [此檢查效能號碼](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 如果使用協力廠商 VPN 閘道，請洽詢廠商以取得效能號碼。

## <a name="next-steps"></a>接下來的步驟
如需 MACsec 設定的詳細資訊，請參閱 [設定 MACsec](expressroute-howto-macsec.md) 。

如需 IPsec 設定的詳細資訊，請參閱 [設定 ipsec](site-to-site-vpn-over-microsoft-peering.md) 。
