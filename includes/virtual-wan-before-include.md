---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a3b9160f1fb28c68bdbaa540c78b2890dbcab82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812659"
---
* 您有一個要連線的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../articles/virtual-network/quick-create-portal.md)。

* 您的虛擬網路沒有任何虛擬網路閘道。 如果您的虛擬網路有閘道 (VPN 或 ExpressRoute)，則必須移除所有閘道。 此設定需要將虛擬網路改為連線到虛擬 WAN 中樞閘道。

* 取得中樞區域的 IP 位址範圍。 中樞是虛擬 WAN 建立和使用的虛擬網路。 您為中樞區域指定的位址範圍不能與任何連線的現有虛擬網路重疊。 也不能與連線至內部部署的位址範圍重疊。 如果您不熟悉位於內部部署網路設定的 IP 位址範圍，請與能夠提供那些詳細資料的人員協調。

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
