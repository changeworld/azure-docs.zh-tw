---
title: Azure 快速路由:關於加密
description: 瞭解快速路由加密。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: d48f83df4e83fd1d1fe38eea76432e1b1c711b76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461458"
---
# <a name="expressroute-encryption"></a>快速路由加密
 
ExpressRoute 支援一些加密技術,以確保網路和Microsoft網路之間遍曆數據的機密性和完整性。

## <a name="point-to-point-encryption-by-macsec-faq"></a>由 MACsec 常見問題解答進行點對點加密
MACsec 是[IEEE 標準](https://1.ieee802.org/security/802-1ae/)。 它在媒體存取控制 (MAC) 級別或網路層 2 加密數據。 當您透過[ExpressRoute Direct](expressroute-erdirect-about.md)連接到 Microsoft 時,您可以使用 MACsec 加密網路設備和微軟網路設備之間的物理連結。 默認情況下,在 ExpressRoute Direct 埠上禁用 MACsec。 您自帶 MACsec 金鑰進行加密,並將儲存[在 Azure 金鑰保存 。](../key-vault/general/overview.md) 您決定何時輪換金鑰。 請參閱下面的其他常見問題解答。
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>我能否在由快遞供應商預配的 ExpressRoute 電路上啟用 MACsec?
否。 MACsec 使用一個實體(即客戶)擁有的密鑰對物理鏈路上的所有流量進行加密。 因此,它僅在 ExpressRoute Direct 上可用。
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>是否可以加密 ExpressRoute Direct 連接埠上的一些 ExpressRoute 電路,並在同一埠上保留其他電路未加密? 
否。 啟用 MACsec 後,所有網路控制流量(例如,BGP 數據流量和客戶數據流量)將被加密。 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>當我啟用/禁用 MACsec 或更新 MACsec 密鑰時,我的本地網路是否會通過 ExpressRoute 失去與 Microsoft 的連接?
是。 對於 MACsec 設定,我們僅支援預共用金鑰模式。 這意味著您需要在您的設備和Microsoft上(透過我們的API)更新金鑰。 此更改不是原子的,因此當兩側存在關鍵不匹配時,您將失去連接。 我們強烈建議您為配置更改安排維護視窗。 為了盡量減少停機時間,我們建議您在將網路流量切換到另一個鏈路后,一次更新 ExpressRoute Direct 的一個鏈路上的配置。  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>如果我的設備與 Microsoft 的 MACsec 金鑰不匹配,流量是否會繼續流動?
否。 如果 MACsec 已配置,並且發生密鑰不匹配,您將失去與 Microsoft 的連接。 換句話說,我們不會回到未加密的連接,公開您的數據。 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>在 ExpressRoute Direct 上啟用 MACsec 是否會降低網路效能?
MACsec 加密和解密發生在我們使用的路由器上的硬體中。 對我們這邊沒有性能影響。 但是,您應該與網路供應商聯繫您使用的設備,並查看 MACsec 是否有任何性能影響。
### <a name="which-cipher-suites-are-supported-for-encryption"></a>加密支援哪些密碼套件?
我們支援 AES128 和 AES256。

## <a name="end-to-end-encryption-by-ipsec-faq"></a>IPsec 常見問題解答的端到端加密
IPsec 是[IETF 標準](https://tools.ietf.org/html/rfc6071)。 它在 Internet 協定 (IP) 級別或網路層 3 對數據進行加密。 可以使用 IPsec 加密本地網路和 Azure 上的虛擬網路 (VNET) 之間的端到端連接。 請參閱下面的其他常見問題解答。
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>除了在快速路由直接埠上啟用 MACsec 之外,我是否可以啟用 IPsec?
是。 MACsec 保護您和Microsoft之間的物理連接。 IPsec 保護您與 Azure 上的虛擬網路之間的端到端連接。 您可以獨立啟用它們。 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>我能否使用 Azure VPN 閘道在本地網路和 Azure 虛擬網路之間設置 IPsec 隧道?
是。 您可以在 ExpressRoute 電路的 Microsoft 對等互連上設置此 IPsec 隧道。 請遵循我們的[設定指南](site-to-site-vpn-over-microsoft-peering.md)。
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>我能否使用 Azure VPN 閘道在 Azure 專用對等互連上設置 IPsec 隧道?
如果採用 Azure 虛擬 WAN,可以按照[以下步驟](../virtual-wan/vpn-over-expressroute.md)加密端到端連接。 如果您有常規的 Azure VNET,則可以在 VNET 中部署第三方 VPN 閘道,並在它和本地 VPN 閘道之間建立 IPsec 隧道。
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>在 ExpressRoute 連接上啟用 IPsec 後,您將獲得哪些輸送量?
如果使用 Azure VPN 閘道,請[在此檢查效能編號](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 如果使用第三方 VPN 閘道,請與供應商聯繫以獲取性能編號。

## <a name="next-steps"></a>後續步驟
有關 MACsec 配置的詳細資訊,請參閱[配置 MACsec。](expressroute-howto-macsec.md)

有關 IPsec 配置的詳細資訊,請參閱[配置 IPsec。](site-to-site-vpn-over-microsoft-peering.md)
