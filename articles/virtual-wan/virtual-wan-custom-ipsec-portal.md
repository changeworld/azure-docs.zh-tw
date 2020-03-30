---
title: 為 Azure 虛擬 WAN 配置自訂 IPsec 策略：門戶 |微軟文檔
description: 瞭解如何使用門戶為 Azure 虛擬 WAN 配置自訂 IPsec 策略。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515742"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>使用門戶為虛擬 WAN 配置自訂 IPsec 策略

您可以在 Azure 門戶中為虛擬 WAN 配置自訂 IPsec 策略。 當您希望雙方（本地和 Azure VPN 閘道）對 IKE 階段 1 和 IKE 階段 2 使用相同的設置時，自訂策略非常有用。

## <a name="working-with-custom-policies"></a>使用自訂策略

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>設定原則

1. **找到虛擬中心**。 從瀏覽器導航到 Azure[門戶](https://aka.ms/azurevirtualwanpreviewfeatures)並使用 Azure 帳戶登錄。 查找網站的虛擬中心。
2. **選擇 VPN 網站**。 從中心頁面中，選擇要為其設置自訂策略的 VPN 網站。

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **編輯 VPN 連接**。 從**內容功能表****..."，** 選擇**編輯VPN連接**。

   ![編輯](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **配置設置**。 在 **"編輯 VPN 連接**"頁上，配置設置。 選擇 **"保存"** 以保存設置。

   ![配置和保存](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。