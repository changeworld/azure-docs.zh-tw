---
title: Azure 虛擬 WAN 合作夥伴和位置 |微軟文檔
description: 本文包含 Azure 虛擬 WAN 合作夥伴和中心位置的清單。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 3976f4114df6222d34f19e2bb44fd05d53057321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123271"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>虛擬 WAN 夥伴與虛擬中樞位置

本文提供有關虛擬 WAN 支援的區域和合作夥伴的資訊，以便連接到虛擬中心。

Azure 虛擬 WAN 是一種網路服務，透過 Azure 提供最佳且自動化的分支對分支連線。 虛擬 WAN 可讓您連線，並設定與 Azure 通訊的分支裝置。 這可以通過手動完成，也可以通過虛擬 WAN 合作夥伴使用提供程式設備來完成。 使用合作夥伴設備可讓您輕鬆使用、簡化連接和建構管理。

從內部部署裝置連線到虛擬中樞是透過自動化方式建立。 虛擬中樞是受 Microsoft 管理的虛擬網路。 中樞包含不同的服務端點，可啟用您內部部署網路 (vpnsite) 中的連線。 每個地區都只能有一個中樞。

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>自動化連線的夥伴

連接到 Azure 虛擬 WAN 的裝置已內建自動化連接能力。 這通常是在裝置管理 UI (或相同功能) 中設定，它可在 VPN 分支裝置與 Azure 虛擬中樞 VPN 端點 (VPN 閘道) 之間設定連線和組態管理。

下列高階自動化是在裝置主控台/管理中心中設定：

* 裝置擁有存取 Azure 虛擬 WAN 資源群組的適當權限
* 將分支裝置上傳到 Azure 虛擬 WAN
* 自動下載 Azure 的連線資訊
* 內部部署分支裝置的設定 

有些連線夥伴可能會擴充自動化功能以包括建立 Azure 虛擬中樞 VNet 與 VPN 閘道的能力。 如果您想瞭解有關自動化的更多資訊，請參閱[虛擬 WAN 合作夥伴的自動化指南](virtual-wan-configure-automation-providers.md)。

## <a name="connectivity-through-partners"></a><a name="partners"></a>通過合作夥伴連接

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

我們近期的路線圖將安排以下合作夥伴：128 技術、阿裡斯塔、阿路巴 HPE、思科系統、F5 網路、開放系統、Oracle SD-WAN、夏普連結和 VMWare Velocloud。

## <a name="locations"></a><a name="locations"></a>位置

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>後續步驟

* 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 常見問題集](virtual-wan-faq.md)。

* 有關如何自動連接到 Azure 虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 合作夥伴的自動化指南](virtual-wan-configure-automation-providers.md)。
