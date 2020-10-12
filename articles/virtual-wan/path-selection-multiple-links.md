---
title: 跨多個 ISP 連結的 Azure 路徑選取範圍
titleSuffix: Azure Virtual WAN
description: 深入瞭解 Azure 路徑選取和虛擬 WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267749"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>跨多個 ISP 連結的 Azure 路徑選取範圍

Azure 虛擬 WAN 提供使用者在 VPN 網站中包含連結資訊的功能，讓 VPN/SD-WAN 裝置解決方案可以設計分支特定原則，以將各種連結的流量引導至 Azure。 這稱為「 **Azure 路徑選取**」。

## <a name="architecture"></a>架構

若要瞭解 Azure 路徑選取的運作方式，讓我們使用虛擬 WAN VPN 網站和站對站連線的範例。

VPN 網站代表內部部署 SD-WAN/VPN 裝置，具有公用 IP、裝置型號和名稱等資訊。實際的內部部署 VPN 網站可能會有多個 ISP 連結，也可以包含在虛擬 WAN VPN 網站資訊中。 這可讓您在 Azure 中查看連結資訊。

進入虛擬 WAN VPN 的站對站 IPsec 連線會在虛擬中樞內的 VPN 閘道實例上終止。 站對站連線代表 VPN 網站和 Azure VPN 閘道之間的連線能力。 它是由一或多個連結連接所組成。 每個連結連線都是由兩個通道所組成，每個通道在 Azure 虛擬 WAN VPN 閘道的唯一實例上終止。 站對站連線中最多可以設定四個連結連線，這可讓您在站對站連線內有最多8個通道。 Azure 最多可支援在單一虛擬 WAN VPN 閘道內終止的2000通道。

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="多重連結圖表":::

下圖顯示連接到 Azure 虛擬 WAN 的網站上的多重連結。 在此圖表中：

* 內部部署分支 (VPN/SD-WAN 裝置) 有兩個 ISP 連結。 每個 ISP 連結都對應至連結連線。

* 它會假設內部部署客戶-管理員 VPN/SD-WAN 裝置支援 IKEv1 或 IKEv2 IPsec。

* 每個 Azure 站對站虛擬 WAN 連線都是由本身內的連結連接所組成。 連接最多可支援四個連結連接。 Azure 會收取虛擬 WAN 連線的連接單位費用。 連結連接不會產生任何費用。

* 接著，每個連結連線都是由兩個可在虛擬 WAN VPN 閘道的不同實例上終止的 IPsec 通道所組成。 閘道會設定為主動-主動閘道以進行復原。 每個連結連線都必須有唯一的 IP 位址和 BGP 對等互連 IP。 在圖表中，通道0可以在實例0上終止，而第1個通道可以在實例1上終止。

* 提供路徑選取的分支裝置可在分支管理解決方案中啟用適當的原則，以將多個連結的流量引導至 Azure。 例如，[ISP 1] 連結可以用於較高優先順序的流量，而 [ISP 2] 連結可以用來做為備份。

* 請務必注意，虛擬中樞 VPN 在所有終止通道之間使用 ECMP (相等的多路徑路由) 。

## <a name="next-steps"></a>後續步驟

請參閱 [AZURE 常見問題](virtual-wan-faq.md)。