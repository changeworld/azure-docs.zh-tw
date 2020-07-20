---
title: Azure 虛擬 WAN 和遠端工作
description: 此頁面說明如何利用 Azure 虛擬 WAN，讓您能夠從遠端工作，因為 COVID-19 流感。
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753774"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure 虛擬 WAN 和支援遠端工作

>[!NOTE]
>本文說明如何運用 Azure 虛擬 WAN、Azure、Microsoft 網路和 Azure 合作夥伴生態系統，從遠端工作，並減輕因 COVID-19 危機而面臨的網路問題。
>

您是否想要為遠端使用者提供連線能力？
您是否突然看到需要支援超過所計畫之使用者的浪湧？
您需要使用者從家裡進行連線，而不只是存取雲端，同時也能連線到內部部署？
您是否需要遠端使用者才能連線到私人 WAN 後方的資源？
您是否需要使用者存取雲端資源，而不需要設定區域之間的連線能力？
由於此全域流感會為我們建立前所未有的變更，因此 Azure 虛擬 WAN 小組在此提供您滿足連線需求的能力。

Azure 虛擬 WAN 是一種網路服務，可將許多網路功能、安全性和路由功能結合在一起，以提供單一操作介面。 這些功能包括分支連線能力（透過來自虛擬 WAN 合作夥伴裝置的連線自動化，例如 SD-WAN 或 VPN CPE）、站對站 VPN 連線能力、遠端使用者 VPN （點對站）連線、私人（ExpressRoute）連線、雲端連線能力、VPN ExpressRoute 互連能力、路由、Azure 防火牆、私人連線加密等等。您不需要擁有所有這些使用案例，就能開始使用虛擬 WAN。 只有一個使用案例可以開始使用，並在您的網路演變時加以調整。

![虛擬 WAN 的圖表](./media/virtual-wan-about/virtualwan1.png)

現在談到遠端使用者，讓我們看看您需要哪些功能才能啟動和執行您的網路：

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>設定遠端使用者連線能力

您可以透過 IPsec/IKE (IKEv2) 或 OpenVPN 連線，來連線到您在 Azure 中的資源。 這種類型的連接需要為遠端使用者設定 VPN 用戶端。 此用戶端可以是[AZURE VPN 用戶端](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN 用戶端或任何支援 IKEv2 的用戶端。 如需詳細資訊，請參閱[建立點對站連線](virtual-wan-point-to-site-portal.md)。

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>從遠端使用者到內部部署的連線能力

您有兩個選項：

* 設定與任何現有 VPN 裝置間的站對站連線能力。 當您將 IPsec VPN 裝置連線到 Azure 虛擬 WAN 中樞時，點對站使用者 VPN （遠端使用者）與站對站 VPN 之間的互連能力會自動進行。 如需有關如何設定從內部部署 VPN 裝置到 Azure 虛擬 WAN 的站對站 VPN 的詳細資訊，請參閱[使用虛擬 Wan 建立站對](virtual-wan-site-to-site-portal.md)站連線。

* 將您的 ExpressRoute 線路連線至虛擬 WAN 中樞。 連接 ExpressRoute 電路需要在虛擬 WAN 中部署 ExpressRoute 閘道。 一旦您部署了一個，點對站使用者 VPN 和 ExpressRoute 使用者之間的互連能力會自動進行。 若要建立 ExpressRoute 連線，請參閱[使用虛擬 WAN 建立 expressroute](virtual-wan-expressroute-portal.md)連線。 您可以使用現有的 ExpressRoute 線路連接到 Azure 虛擬 WAN。

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>現有的基本虛擬 WAN 客戶

基本虛擬 WAN 只提供站對站 VPN。 為了讓遠端使用者能夠連線，您必須將虛擬 WAN 升級為標準虛擬 WAN。 如需升級虛擬 WAN 的步驟，請參閱將[虛擬 wan 從基本升級至標準](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>其他資訊

虛擬 WAN 支援每個區域/位置有一個中樞。 如需位置資訊，請參閱[虛擬 WAN 夥伴與位置](virtual-wan-locations-partners.md)一文。 每個中樞最多支援10000個遠端使用者連線、1000分支連線、四個 ExpressRoute 線路，以及最多500虛擬網路連線。 當您相應增加遠端使用者時，如果您有任何問題，請不要將電子郵件傳送至來尋求協助 azurevirtualwan@microsoft.com 。 如果您需要技術支援，請務必從 Azure 入口網站開啟支援票證，並將會提供說明。

## <a name="faq"></a><a name="faq"></a>常見問題集

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>後續步驟

如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 wan 總覽](virtual-wan-about.md)