---
title: 關於升級線路頻寬 |Azure ExpressRoute
description: 在本文中，瞭解升級 ExpressRoute 線路頻寬的最佳作法
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: duau
ms.openlocfilehash: 7831e7944321e074c312853e1534c47970ebdfdb
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397945"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>關於升級 ExpressRoute 線路頻寬

ExpressRoute 可讓您對 Microsoft 的全球網路進行專用和私人連線。 ExpressRoute 合作夥伴的網路會促進連線，或直接連線到 Microsoft Enterprise Edge (MSEE) 裝置。 設定並測試實體連線後，您可以藉由建立 ExpressRoute 線路和設定對等互連，來啟用第2層和第3層連線能力。

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>升級線路頻寬

為了升級線路頻寬，ExpressRoute Direct 或 ExpressRoute 夥伴必須有 [足夠的可用頻寬](#considerations) ，才能成功升級。

如果有可用的容量，您可以使用下列方法來升級線路：

* [Azure 入口網站](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>容量考量

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>ExpressRoute 夥伴頻寬不足

如果 ExpressRoute 夥伴沒有足夠的容量，您必須建立新的線路，並將其設定為所需的頻寬。 為了維持連線能力，請不要刪除舊的線路，直到新建立的線路布建完成為止，已設定對等互連，並 (有關私人對等互連) 已布建 ExpressRoute 虛擬網路閘道的連線物件。

如果您的 ExpressRoute 夥伴沒有足夠的可用容量，您需要在所需的對等互連位置要求額外的容量。 布建新的容量之後，您可以使用 [ [升級線路頻寬](#upgrade) ] 區段中的文章所包含的步驟來建立新的線路、設定連線能力，以及刪除舊的線路。


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>ExpressRoute Direct 頻寬不足

如果 ExpressRoute Direct 沒有足夠的容量，您可以刪除與不再需要的 ExpressRoute Direct 資源相關聯的線路，或建立新的 ExpressRoute Direct 資源。 如需管理 ExpressRoute Direct 資源的指引，請參閱 [如何設定 Expressroute direct](how-to-expressroute-direct-portal.md)。

## <a name="next-steps"></a>接下來的步驟

* [建立及修改電路](expressroute-howto-circuit-portal-resource-manager.md)
* [建立和修改對等互連組態](expressroute-howto-routing-portal-resource-manager.md)
* [將虛擬網路連結到 ExpressRoute 線路](expressroute-howto-linkvnet-portal-resource-manager.md)
