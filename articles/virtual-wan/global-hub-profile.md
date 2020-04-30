---
title: 下載 Azure 虛擬 WAN 全域或以中樞為基礎的 VPN 設定檔 |Microsoft Docs
description: 了解虛擬 WAN 自動化可調整的分支對分支連線、可用的區域和夥伴。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: b63bb861f4df087f852bb1bf599d32100d063f7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733175"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>為使用者 VPN 用戶端下載以全域或中樞為基礎的設定檔

Azure 虛擬 WAN 為遠端使用者提供兩種類型的連線能力：全域和中樞型。 請使用下列各節來瞭解並下載設定檔。 

> [!IMPORTANT]
> RADIUS 驗證僅支援以中樞為基礎的設定檔。

## <a name="global-profile"></a>全域設定檔

此設定檔會指向包含所有作用中使用者 VPN 中樞的負載平衡器。 使用者會被導向至最接近使用者地理位置的中樞。 當使用者經常前往不同位置時，這種類型的連線很有用。 若要下載**全域**設定檔：

1. 瀏覽至虛擬 WAN。
2. 按一下 [**使用者 VPN**設定]。
3. 反白顯示您要下載設定檔的設定。
4. 按一下 [**下載虛擬 WAN 使用者 VPN 設定檔**]。

   ![全域設定檔](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>以中樞為基礎的設定檔

此設定檔會指向單一中樞。 使用者只能使用此設定檔連接到特定中樞。 若要下載以**中樞為基礎**的設定檔：

1. 瀏覽至虛擬 WAN。
2. 按一下 [總覽] 頁面中的 [**中樞**]。

    ![中樞設定檔1](./media/global-hub-profile/hub1.png)
3. 按一下 **[使用者 VPN （點對站）**]。
4. 按一下 [**下載虛擬中樞使用者 VPN 設定檔**]。

   ![中樞設定檔2](./media/global-hub-profile/hub2.png)
5. 檢查**EAPTLS**。
6. 按一下 [**產生並下載設定檔**]。

   ![中樞設定檔3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
