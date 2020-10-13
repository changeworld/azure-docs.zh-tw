---
title: 將 Azure 連接到公用雲端 |Microsoft Docs
description: 描述將 Azure 連接到其他公用雲端的各種方式
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: f413b6100fb2a7d1c8bc06d787468cc3e79c0169
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397469"
---
# <a name="connecting-azure-with-public-clouds"></a>使用公用雲端連接 Azure

許多企業都因為商務和技術目標而追求多重雲端的策略。 這些包括成本、彈性、功能可用性、冗余、資料主權等等。這項策略可協助他們充分利用兩個雲端。 

這種方法也會在網路和應用程式架構方面造成企業面臨的挑戰。 其中一些挑戰是延遲和資料輸送量。 為了解決這些挑戰，客戶想要直接連接到多個雲端。 某些服務提供者會提供解決方案來為客戶連接多個雲端提供者。 在其他情況下，客戶可以部署自己的路由器來連接多個公用雲端。
## <a name="connectivity-via-expressroute"></a>透過 ExpressRoute 的連線能力
ExpressRoute 可讓客戶透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 使用 ExpressRoute 時，客戶可以建立與 Microsoft 雲端服務的連線。

有三種方式可以透過 ExpressRoute 連線。

1. 層提供者
2. L2 提供者
3. 直接連接

### <a name="layer3-provider"></a>層提供者

層提供者通常稱為 IP VPN 或 MPLS VPN 提供者。 客戶會將這些提供者用於其資料中心、分支與雲端之間的 multipoint 連線能力。 客戶透過 BGP 或透過靜態預設路由連接至 L3 提供者。 服務提供者會在客戶網站、資料中心和公用雲端之間通告路由。 
 
透過層提供者連線時，Microsoft 會透過 BGP 將客戶 VNET 路由公告至服務提供者。 提供者可以有兩個不同的實作為。

![顯示層提供者的圖表。](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

如果來自所有雲端提供者的流量都會抵達客戶路由器，則提供者可能會在個別的 VRF 中登陸每個雲端提供者。 如果客戶是使用服務提供者來執行 BGP，則預設會將這些路由重新通告給其他雲端提供者。 

如果服務提供者將所有雲端提供者都登陸在相同的 VRF 中，則路由會直接從服務提供者公告至其他雲端提供者。 這是假設標準 BGP 作業，其中 eBGP 路由預設會公告至其他 eBGP 鄰居。

每個公用雲端都有不同的前置詞限制，因此散發路由服務提供者時，應謹慎發佈路由。

### <a name="layer2-provider-and-direct-connection"></a>L2 提供者和直接連接

雖然這兩個模型中的實體連線不同，但在層 BGP 是直接建立在 MSEE 與客戶路由器之間。 若為 ExpressRoute Direct 客戶，則直接連線到 MSEE。 在 L2 的情況下，服務提供者會將 VLAN 從客戶的內部部署延伸至雲端。 客戶會在第2層網路之上執行 BGP，以將其 Dc 連線到雲端。
![顯示 L2 提供者和直接連接的圖表。](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
在這兩種情況下，客戶都將對每個公用雲端進行點對點連線。 客戶會建立與每個公用雲端的個別 BGP 連線。 依預設，一個雲端提供者收到的路由將會公告至其他雲端提供者。 每個雲端提供者都有不同的前置詞限制，因此在廣告路由時，客戶應該負責處理這些限制。 客戶可以搭配 Microsoft 使用一般的 BGP 旋鈕，同時通告來自其他公用雲端的路由。

## <a name="direct-connection-with-expressroute"></a>直接連線至 ExpressRoute

客戶可以選擇直接將 ExpressRoute 連線到雲端提供者的直接連線供應專案。 兩個雲端提供者將會彼此連線，而且會在其路由器之間直接建立 BGP。 這種類型的連接目前可用於 Oracle。

## <a name="site-to-site-vpn"></a>站對站 VPN

客戶可以利用網際網路將其在 Azure 中的實例與其他公用雲端連線。 幾乎所有的雲端提供者都提供站對站 VPN 功能。 不過，因為缺少特定的變異數，所以可能不相容。 例如，有些雲端提供者只支援 IKEv1，因此該雲端中需要有 VPN 終止端點。 針對支援 IKEv2 的雲端提供者，可在兩個雲端提供者的 VPN 閘道之間建立直接通道。

站對站 VPN 不會被視為高輸送量和低延遲的解決方案。 不過，它可以用來做為實體連接的備份。

## <a name="next-steps"></a>後續步驟
如需 ExpressRoute 和虛擬網路連線能力的任何進一步問題，請參閱 [EXPRESSROUTE 常見問題][ER-FAQ] 。

請參閱 [設定 azure 與 Oracle 雲端之間的直接][ER-OCI] 連線，以連線到 Azure 與 oracle 之間的連線能力

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



