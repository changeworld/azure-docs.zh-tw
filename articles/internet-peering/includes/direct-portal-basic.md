---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774636"
---
1. 按一下 **"創建資源** > **查看所有**"。

    > [!div class="mx-imgBorder"]
    > ![搜索對等互連](../media/setup-seeall.png)

1. 在搜索框中搜索*對等，* 然後按鍵盤上的 *"輸入*"。 從結果中，按一下**對等資源**。

    > [!div class="mx-imgBorder"]
    > ![啟動對等互連](../media/setup-launch.png)

1. 啟動**對等互連**後，請查看頁面以瞭解詳細資訊。 準備就緒後，按一下"**創建**"。

    > [!div class="mx-imgBorder"]
    > ![創建對等互連](../media/setup-create.png)

1. 在"**創建對等"** 頁上，在 **"基本"** 選項卡下填寫欄位，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等基礎知識](../media/setup-basics-tab.png)

    * 選擇 Azure**訂閱**。
    * 對於**資源組**，可以從下拉清單中選擇現有資源組，也可以按一下"**創建新**"來創建新組。 我們將為此示例創建新的資源組。
    * **名稱**對應于資源名稱，可以是您選擇的任何名稱。
    * 如果在上述步驟中選擇現有資源組，則**自動選擇區域**。 如果選擇創建新資源組，則還需要選擇希望資源駐留的 Azure 區域。 美國東部

        > [!NOTE]
        > 資源組所在的區域與要與 Microsoft 創建對等互連的位置無關。 但最佳做法是，在駐留在最近的 Azure 區域的資源組中組織對等資源。 例如：對於 Ashburn 中的對等互連，您可以在*美國東部*或*US2 中*創建資源組

    * 在 **"對等 ASN"欄位中選擇 ASN。**

        > [!IMPORTANT]
        > * 在提交對等請求之前，您只能選擇具有驗證狀態的 ASN 作為"已批准"。 如果您剛剛提交了您的 PeerAsn 請求，請等待 12 小時左右，以便 ASN 關聯"已批准"。 如果您選擇的 ASN 正在等待驗證，您將看到一條錯誤訊息。 
        > * 如果看不到需要選擇的 ASN，請檢查您是否選擇了正確的訂閱。 如果是這樣，請檢查是否已使用[對等方 ASN 創建了](../howto-subscription-association-portal.md)對等 Asn 到 Azure 訂閱 。

        > [!div class="mx-imgBorder"]
        > ![已填充對等基礎知識](../media/setup-direct-basics-filled-tab.png)

    * 按一下 **"下一步：配置>** 繼續。
