---
title: 設定 Azure 虛擬 WAN 的自訂 IPsec 原則：入口網站 |Microsoft Docs
description: 瞭解如何使用入口網站設定 Azure 虛擬 WAN 的自訂 IPsec 原則。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 0ea4523d1558f6887e1aef344198026591dac617
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84752622"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>使用入口網站設定虛擬 WAN 的自訂 IPsec 原則

您可以在 Azure 入口網站中設定虛擬 WAN 的自訂 IPsec 原則。 當您想要兩側（內部部署和 Azure VPN 閘道）使用相同的 IKE 階段1和 IKE 階段2設定時，自訂原則會很有説明。

## <a name="working-with-custom-policies"></a>使用自訂原則

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>設定原則

1. **找出虛擬中樞**。 透過瀏覽器瀏覽至 [Azure 入口網站](https://aka.ms/azurevirtualwanpreviewfeatures) ，並使用您的 Azure 帳戶登入。 找出您網站的虛擬中樞。
2. **選取 VPN 網站**。 從 [中樞] 頁面中，選取您要設定自訂原則的 VPN 網站。

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **編輯 VPN**連線。 從**內容功能表****中，選取**[**編輯 VPN**連線]。

   ![編輯](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. 進行**設定**。 在 [**編輯 VPN**連線] 頁面上，設定設定值。 選取 [儲存] 以儲存您的設定。

   ![設定並儲存](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。