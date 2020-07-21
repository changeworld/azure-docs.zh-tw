---
title: 關於升級線路頻寬 |Azure ExpressRoute
description: 在本文中，您將瞭解升級 ExpressRoute 線路頻寬的最佳作法
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: a8f5aaa7b2a054aa31198779414387cebf0f0fbd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537032"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>關於升級 ExpressRoute 線路頻寬

ExpressRoute 可讓您對 Microsoft 的全球網路提供專用和私人連線能力。 連線能力是由 ExpressRoute 合作夥伴的網路，或與 Microsoft Enterprise Edge （MSEE）裝置的直接連線所促成。 設定並測試實體連線之後，您可以藉由建立 ExpressRoute 線路及設定對等互連來啟用第2層和第3層連線能力。

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>升級線路頻寬

為了升級線路頻寬，ExpressRoute Direct 或 ExpressRoute 合作夥伴必須有[足夠的可用頻寬](#considerations)，才能成功升級。

如果有可用的容量，您可以使用下列方法來升級線路：

* [Azure 入口網站](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>容量考量

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>ExpressRoute 合作夥伴頻寬不足

如果 ExpressRoute 合作夥伴沒有足夠的容量，您必須建立新的線路，並將其設定為所需的頻寬。 為了維持連線，請不要刪除舊的迴圈，直到布建新建立的線路、已設定對等互連，以及（關於私用對等互連）已布建 ExpressRoute 虛擬網路閘道的連線物件為止。

如果您的 ExpressRoute 合作夥伴沒有足夠的可用容量，您必須在想要的對等互連位置要求額外的容量。 布建新容量之後，您可以使用 [[升級線路頻寬](#upgrade)] 區段中的文章所包含的步驟來建立新的線路、設定連線能力，以及刪除舊的線路。


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>ExpressRoute Direct 頻寬不足

如果 ExpressRoute Direct 沒有足夠的容量，您可以刪除已不再需要之 ExpressRoute Direct 資源的相關電路，或建立新的 ExpressRoute Direct 資源。 如需管理 ExpressRoute Direct 資源的指引，請參閱[如何設定 Expressroute direct](how-to-expressroute-direct-portal.md)。

## <a name="next-steps"></a>後續步驟

* [建立及修改電路](expressroute-howto-circuit-portal-resource-manager.md)
* [建立和修改對等互連組態](expressroute-howto-routing-portal-resource-manager.md)
* [將虛擬網路連結到 ExpressRoute 線路](expressroute-howto-linkvnet-portal-resource-manager.md)
