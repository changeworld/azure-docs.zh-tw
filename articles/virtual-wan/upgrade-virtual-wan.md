---
title: 將 Azure 虛擬 WAN 從基本版級升級到標準 - Azure 門戶 |微軟文檔
description: 您可以升級虛擬 WAN 類型以獲得更大的功能。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515807"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>將虛擬廣域網路從基本網域升級到標準

本文可説明您將基本 WAN 升級到標準 WAN。 "基本"WAN 類型將其中的所有集線器創建為基本 SKU 集線器。 在基本中心中，您只能使用網站到網站 VPN 功能。 "標準"WAN 類型將其中的所有集線器創建為標準 SKU 集線器。 使用標準集線器時，可以啟用 ExpressRoute、使用者（點對點）VPN、全網狀集線器和 VNet 到 VNet 傳輸通過 Azure 集線器。

下表顯示了每種 WAN 類型的可用配置：

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>更改虛擬 WAN 類型

1. 在虛擬 WAN 的頁面上，選擇 **"配置"** 以打開"配置"頁。

   ![虛擬 WAN 的圖表](./media/upgrade-virtual-wan/1.png)
2. 對於虛擬廣域網路類型，請從下拉清單中選擇 **"標準**"。

   ![虛擬 WAN 的圖表](./media/upgrade-virtual-wan/2.png)
3. 請注意，如果升級到標準虛擬 WAN，則無法恢復為基本虛擬 WAN。 選擇 **"確認**是否要升級"。

   ![虛擬 WAN 的圖表](./media/upgrade-virtual-wan/4.png)
4. 保存更改後，虛擬 WAN 頁面看起來與本示例類似。

   ![虛擬 WAN 的圖表](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。