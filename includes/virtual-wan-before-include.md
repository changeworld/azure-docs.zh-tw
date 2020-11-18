---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359490"
---
* 您有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 您有一個要連線的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../articles/virtual-network/quick-create-portal.md)文章。

* 您的虛擬網路必須沒有任何現有的虛擬網路閘道。 如果虛擬網路已經有閘道 (VPN 或 ExpressRoute) ，您必須先移除所有閘道，然後再繼續。 此組態需要讓虛擬網路只連線到虛擬 WAN 中樞閘道。

* 虛擬中樞是虛擬 WAN 建立和使用的虛擬網路。 這是您在區域中的虛擬 WAN 網路。 取得虛擬中樞區域的 IP 位址範圍。 您為中樞區域指定的位址範圍不能與任何連線的現有虛擬網路重疊。 也不能與您連線至內部部署的位址範圍重疊。 如果您不熟悉位於內部部署網路設定的 IP 位址範圍，請與能夠提供那些詳細資料的人員協調。
