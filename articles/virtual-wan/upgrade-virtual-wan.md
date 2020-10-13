---
title: 將 Azure 虛擬 WAN 從基本升級至標準-Azure 入口網站 |Microsoft Docs
description: 您可以升級您的虛擬 WAN 類型，以獲得更高的功能。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447356"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>將虛擬 WAN 從基本升級至標準

本文可協助您將基本 WAN 升級至標準的 WAN。 「基本」 WAN 類型會將其內部的所有中樞建立為基本 SKU 中樞。 在基本中樞內，您僅限使用站對站 VPN 功能。 「標準」 WAN 類型會建立其內部的所有中樞作為標準 SKU 中樞。 當您使用標準中樞時，您可以啟用 ExpressRoute、使用者 (點對站) VPN、完整網狀中樞，以及透過 Azure 中樞的 VNet 對 VNet 傳輸。

下表顯示每種 WAN 類型可用的設定：

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>變更虛擬 WAN 類型

1. 在虛擬 WAN 的頁面上 **，選取 [** 設定] 以開啟 [設定] 頁面。

   ![顯示 [設定] 頁面的螢幕擷取畫面，其中包含有關升級至底部醒目提示之標準類型虛擬 WAN 的 [資訊] 文字方塊。](./media/upgrade-virtual-wan/1.png)
2. 針對虛擬 WAN 類型，從下拉式清單中選取 [ **標準** ]。

   ![顯示「虛擬 WAN 類型」下拉式功能表的螢幕擷取畫面。](./media/upgrade-virtual-wan/2.png)
3. 請注意，如果您升級至標準虛擬 WAN，則無法還原回基本虛擬 WAN。 如果您要升級，請選取 [ **確認** ]。

   ![顯示 [升級確認] 對話方塊的螢幕擷取畫面。](./media/upgrade-virtual-wan/4.png)
4. 儲存變更之後，您的虛擬 WAN 頁面看起來類似此範例。

   ![虛擬 WAN 的圖表](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。