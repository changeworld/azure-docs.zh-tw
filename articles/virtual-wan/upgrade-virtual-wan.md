---
title: 將 Azure 虛擬 WAN 從基本升級至標準-Azure 入口網站 |Microsoft Docs
description: 您可以升級您的虛擬 WAN 類型，以提供更大的功能。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 769aa6c0d546b7a9bcbe355136bad811fb4f47b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753315"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>將虛擬 WAN 從基本升級為標準

本文可協助您將基本 WAN 升級為標準 WAN。 「基本」 WAN 類型會將其內部的所有中樞建立為基本 SKU 中樞。 在基本中樞中，您僅限使用站對站 VPN 功能。 「標準」 WAN 類型會建立其內部的所有中樞作為標準 SKU 中樞。 當您使用標準中樞時，您可以啟用 ExpressRoute、使用者（點對站） VPN、完整網狀中樞，以及透過 Azure 中樞的 VNet 對 VNet 傳輸。

下表顯示適用于每種 WAN 類型的設定：

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>變更虛擬 WAN 類型

1. 在您的虛擬 WAN 頁面上 **，選取 [** 設定] 以開啟 [設定] 頁面。

   ![虛擬 WAN 的圖表](./media/upgrade-virtual-wan/1.png)
2. 針對 [虛擬 WAN 類型]，從下拉式清單中選取 [**標準**]。

   ![虛擬 WAN 的圖表](./media/upgrade-virtual-wan/2.png)
3. 請瞭解，如果您升級至標準虛擬 WAN，則無法還原為基本的虛擬 WAN。 如果您想要升級，請選取 [**確認**]。

   ![虛擬 WAN 的圖表](./media/upgrade-virtual-wan/4.png)
4. 儲存變更之後，您的虛擬 WAN 頁面看起來會類似此範例。

   ![虛擬 WAN 的圖表](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。