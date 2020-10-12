---
title: 下載 Azure 虛擬 WAN 全域或以中樞為基礎的 VPN 設定檔 |Microsoft Docs
description: 瞭解 Azure 虛擬 WAN 如何為遠端使用者提供兩種類型的連線能力，以及如何下載設定檔。
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: 6d5c4ba0f1f55119d1ec38296e67ae3e90c52650
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313717"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>為使用者 VPN 用戶端下載全域或以中樞為基礎的設定檔

Azure 虛擬 WAN 為遠端使用者提供兩種類型的連線能力：全域和中樞型。 請使用以下各節來瞭解和下載設定檔。 

> [!IMPORTANT]
> RADIUS 驗證僅支援以中樞為基礎的設定檔。

## <a name="global-profile"></a>全域設定檔

設定檔會指向包含所有作用中使用者 VPN 中樞的負載平衡器。 使用者會被導向到最接近使用者地理位置的中樞。 當使用者經常前往不同的位置時，這種類型的連線會很有用。 若要下載 **全域** 設定檔：

1. 瀏覽至虛擬 WAN。
2. 按一下 [ **使用者 VPN**設定]。
3. 反白顯示您要下載設定檔的設定。
4. 按一下 [ **下載虛擬 WAN 使用者 VPN 設定檔**]。

   ![全域設定檔](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>以中樞為基礎的設定檔

設定檔會指向單一中樞。 使用者只能使用此設定檔連接到特定的中樞。 下載以 **中樞為基礎** 的設定檔：

1. 瀏覽至虛擬 WAN。
2. 按一下 [總覽] 頁面中的 [ **中樞** ]。

    ![中樞設定檔1](./media/global-hub-profile/hub1.png)
3. 按一下 [ **使用者 VPN] (指向 [網站]) **。
4. 按一下 [ **下載虛擬中樞使用者 VPN 設定檔**]。

   ![中樞設定檔2](./media/global-hub-profile/hub2.png)
5. 檢查 **EAPTLS**。
6. 按一下 [ **產生並下載設定檔**]。

   ![中樞設定檔3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
