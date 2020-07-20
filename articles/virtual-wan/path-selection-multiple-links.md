---
title: 跨多個 ISP 連結的 Azure 路徑選取範圍
titleSuffix: Azure Virtual WAN
description: 瞭解 Azure 路徑選取和虛擬 WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: cherylmc
ms.openlocfilehash: a83a050b65f5673a86dd07ec13842f6009d2c8dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753348"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>跨多個 ISP 連結的 Azure 路徑選取範圍

Azure 虛擬 WAN 可讓使用者在 VPN 網站中包含連結資訊，讓 VPN/SD WAN 裝置解決方案可以設計分支特定原則，以將各種連結的流量引導至 Azure。 這稱為「 **Azure 路徑選取**」。

## <a name="architecture"></a>架構

若要瞭解 Azure 路徑選取的運作方式，讓我們使用虛擬 WAN VPN 網站和站對站連線的範例。

VPN 網站代表內部部署 SD-WAN/VPN 裝置，其中包含公用 IP、裝置型號和名稱等資訊。實際的內部部署 VPN 網站可能會有多個 ISP 連結，也可以包含在虛擬 WAN VPN 網站資訊中。 這可讓您在 Azure 中查看連結資訊。

進入虛擬 WAN VPN 的站對站 IPsec 連線會在虛擬中樞內的 VPN 閘道實例上終止。 站對站連線代表 VPN 網站和 Azure VPN 閘道之間的連線。 其中包含一個或多個連結連接。 每個連結連線都包含兩個通道，其中每個通道在 Azure 虛擬 WAN VPN 閘道的唯一實例上終止。 最多可以在站對站連線中設定四個連結連線，這可讓站對站連線內最多有八個通道。 Azure 最多可支援在單一虛擬 WAN VPN 閘道中終止2000個通道。

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="多重連結圖表":::

此圖顯示連接到 Azure 虛擬 WAN 之網站的多重連結。 在此圖表中：

* 內部部署分支（VPN/SD-WAN 裝置）上有兩個 ISP 連結。 每個 ISP 連結都會對應至連結連線。

* 它假設內部部署客戶-管理員 VPN/SD WAN 裝置支援 IKEv1 或 IKEv2 IPsec。

* 每個 Azure 站對站虛擬 WAN 連線都是由內的連結連線組成。 連接最多支援四個連結連接。 Azure 會向虛擬 WAN 連線收取連接單位費用。 連結連接不收費。

* 每個連結連線都包含兩個可在虛擬 WAN VPN 閘道的兩個不同實例上終止的 IPsec 通道。 閘道會設定為主動-主動閘道以進行復原。 每個連結連線都必須要有唯一的 IP 位址和 BGP 對等互連 IP。 在圖表中，通道0可以在實例0上終止，而通道1可以在實例1上終止。

* 提供直接選取的分支裝置可以在分支管理解決方案中啟用適當的原則，以將多個連結的流量引導至 Azure。 例如，ISP 1 連結可用於較高優先順序的流量，而 ISP 2 連結則可用來做為備份。

* 請務必注意，虛擬中樞 VPN 會在所有終止通道上使用 ECMP （等於成本多路徑路由）。

## <a name="next-steps"></a>後續步驟

請參閱[AZURE 常見問題](virtual-wan-faq.md)。