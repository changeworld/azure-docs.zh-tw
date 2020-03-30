---
title: 下載 Azure 虛擬 WAN 全域或基於集線器的 VPN 設定檔 |微軟文檔
description: 了解虛擬 WAN 自動化可調整的分支對分支連線、可用的區域和夥伴。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965225"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>下載使用者 VPN 用戶端的全域或基於中心的設定檔

Azure 虛擬 WAN 為遠端使用者提供兩種類型的連接：全域和基於集線器。 使用以下部分瞭解和下載設定檔。

## <a name="global-profile"></a>全球概況

設定檔指向包含所有活動使用者 VPN 集線器的負載等化器。 使用者被定向到最接近使用者的地理位置的中心。 當使用者頻繁前往不同位置時，這種類型的連接非常有用。 要下載**全域**設定檔：

1. 瀏覽至虛擬 WAN。
2. 按一下**使用者 VPN 配置**。
3. 突出顯示要為其下載設定檔的配置。
4. 按一下 **"下載虛擬 WAN 使用者 VPN 設定檔**"。

   ![全球概況](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>基於集線器的設定檔

設定檔指向單個中心。 使用者只能使用此設定檔連接到特定中心。 要下載**基於集線器的**設定檔：

1. 瀏覽至虛擬 WAN。
2. 按一下"概述"頁中的 **"中心**"。

    ![集線器設定檔 1](./media/global-hub-profile/hub1.png)
3. 按一下**使用者 VPN（指向網站）**。
4. 按一下 **"下載虛擬中心使用者 VPN 設定檔**"。

   ![集線器設定檔 2](./media/global-hub-profile/hub2.png)
5. 檢查**EAPTLS**。
6. 按一下 **"生成並下載設定檔**"。

   ![集線器設定檔 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
