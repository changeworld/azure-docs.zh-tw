---
title: 將 Azure 連接到公用雲端 |Microsoft Docs
description: 說明將 Azure 連線至其他公用雲端的各種方式
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: 80863b56334b0d2d76cdf505dcd15c5cc4c14c52
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081095"
---
# <a name="connecting-azure-with-public-clouds"></a>連接 Azure 與公用雲端

許多企業都因為商務和技術目標，而追求了多雲端的策略。 其中包括成本、彈性、功能可用性、冗余、資料主權等。此策略可協助他們充分利用這兩個雲端。 

這種方法也會在網路和應用程式架構方面面臨企業面臨的挑戰。 其中一些挑戰是延遲和資料輸送量。 為了解決客戶想要直接連接到多個雲端的挑戰。 有些服務提供者會提供解決方案，以連接客戶的多個雲端提供者。 在其他情況下，客戶可以部署自己的路由器來連接多個公用雲端。
## <a name="connectivity-via-expressroute"></a>透過 ExpressRoute 的連線能力
ExpressRoute 可讓客戶透過連線提供者所提供的私人連線，將其內部部署網路延伸至 Microsoft 雲端。 透過 ExpressRoute，客戶可以建立與 Microsoft 雲端服務的連線。

有三種方式可透過 ExpressRoute 連接。

1. 層提供者
2. L2 提供者
3. 直接連接

### <a name="layer3-provider"></a>層提供者

層提供者通常稱為 IP VPN 或 MPLS VPN 提供者。 客戶會在其資料中心、分支和雲端之間，利用這些提供者來進行 multipoint 連線。 客戶透過 BGP 或透過靜態預設路由連接到 L3 提供者。 服務提供者會在客戶網站、資料中心和公用雲端之間通告路由。 
 
透過層提供者連線時，Microsoft 會將客戶 VNET 路由通告給服務提供者的 BGP。 提供者可以有兩個不同的執行方式。

![顯示層提供者的圖表。](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

提供者可能會在不同的 VRF 中登陸每個雲端提供者，如果來自所有雲端提供者的流量會到達客戶路由器。 如果客戶與服務提供者一起執行 BGP，則預設會將這些路由重新公告至其他雲端提供者。 

如果服務提供者在同一個 VRF 中登陸所有的雲端提供者，則路由會直接從服務提供者通告給其他雲端提供者。 這是假設標準 BGP 作業，其中預設會將 eBGP 路由公告至其他 eBGP 鄰近專案。

每個公用雲端都有不同的前置詞限制，因此在散發路由服務提供者時，應特別注意散發路由。

### <a name="layer2-provider-and-direct-connection"></a>L2 提供者和直接連接

雖然這兩種模型中的實體連線不同，但在層 BGP 是直接在 MSEE 與客戶路由器之間建立。 針對 ExpressRoute Direct 客戶，會直接連線至 MSEE。 如果是 L2，服務提供者會將 VLAN 從客戶的內部部署擴充至雲端。 客戶在第2方網路上執行 BGP，以將其 Dc 連線到雲端。
![顯示 L2 提供者和直接連接的圖表。](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
在這兩種情況下，客戶都會有每個公用雲端的點對點連線。 客戶會建立與每個公用雲端的個別 BGP 連線。 依預設，一個雲端提供者接收到的路由將會公告至其他雲端提供者。 每個雲端提供者都有不同的前置詞限制，因此在通告路由時，客戶應負責這些限制。 客戶可以使用一般 BGP 旋鈕與 Microsoft，同時從其他公用雲端公告路由。

## <a name="direct-connection-with-expressroute"></a>ExpressRoute 的直接連線

客戶可以選擇直接將 ExpressRoute 連線到雲端提供者的直接連線供應專案。 兩個雲端提供者會連線回到後端，而 BGP 會在其路由器之間直接建立。 這種類型的連接目前可與 Oracle 搭配使用。

## <a name="site-to-site-vpn"></a>站對站 VPN

客戶可以利用網際網路來連接其在 Azure 中的實例與其他公用雲端。 幾乎所有的雲端提供者都提供站對站 VPN 功能。 不過，因為缺少特定的變異數，所以可能會發生不相容的情況。 例如，有些雲端提供者只支援 IKEv1，因此該雲端中需要 VPN 終止端點。 對於支援 IKEv2 的雲端提供者，可以在兩個雲端提供者的 VPN 閘道上建立直接通道。

站對站 VPN 不會被視為高輸送量和低延遲解決方案。 不過，它可以用來做為實體連線的備份。

## <a name="next-steps"></a>後續步驟
如需 ExpressRoute 和虛擬網路連線能力的任何進一步問題，請參閱[EXPRESSROUTE 常見問題][ER-FAQ]。

如需 Azure 與 Oracle 之間的連線能力，請參閱[設定 azure 和 Oracle 雲端之間的直接][ER-OCI]連線

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



