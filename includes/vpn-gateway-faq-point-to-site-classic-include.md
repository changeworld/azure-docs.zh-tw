---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c1e34bb418f9be2e26afc117343f1fa50bd8566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76308968"
---
這個常見問題集適用於使用傳統部署模型的 P2S 連線。

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>可以使用哪些用戶端作業系統來搭配點對站台？

以下為支援的用戶端作業系統：

* Windows 7 (32 位元和 64 位元)
* Windows Server 2008 R2 (僅限 64 位元)
* Windows 8 (32 位元和 64 位元)
* Windows 8.1 (32 位元和 64 位元)
* Windows Server 2012 (僅限 64 位元)
* Windows Server 2012 R2 (僅限 64 位元)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>是否任何支援 SSTP 的軟體 VPN 用戶端都適用於點對站功能？

否。 僅支援列出的 Windows 作業系統版本。

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>在我的點對站組態中可以有多少個 VPN 用戶端端點？

VPN 用戶端終結點的數量取決於您的閘道 sKU 和協定。
[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>是否可以使用自己的內部 PKI 根 CA 進行點對站台連線？

是。 先前只能使用自我簽署的根憑證。 您仍可將 20 個根憑證上傳。

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>是否可以使用點對站功能周遊 Proxy 和防火牆？

是。 我們使用安全通訊端通道通訊協定 (SSTP) 穿過防火牆。 此通道將會顯示為 HTTPS 連線。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>如果我重新啟動針對點對站台設定的用戶端電腦，VPN 將自動重新連線嗎？

用戶端電腦預設為不會自動重新建立 VPN 連線。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>在 VPN 用戶端上點對站功能支援自動重新連線和 DDNS 嗎？

否。 點對站 VPN 目前不支援自動重新連接和 DDNS。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>可以對相同虛擬網路使用站對站和點對站組態嗎？

是。 如果閘道是路由式 VPN 類型，這兩個解決方案都可正常運作。 如果是傳統部署模型，則需要動態閘道。 靜態路由 VPN 閘道或使用 **-VpnType PolicyBased** Cmdlet 的閘道不支援點對站。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>是否可以將點對站台用戶端設定為同時連接到多個虛擬網路？

是。 但是虛擬網路不能有重疊的 IP 前置詞，而且點對站台位址空間在虛擬網路之間不得重疊。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>透過網站間或點對站台連線可以獲得多少輸送量？

很難維護 VPN 通道的確切輸送量。 IPsec 和 SSTP 為加密嚴謹的 VPN 通訊協定。 輸送量也會受限於內部部署與網際網路之間的延遲和頻寬。
