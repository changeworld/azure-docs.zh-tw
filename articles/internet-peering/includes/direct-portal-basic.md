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
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678542"
---
1. 選擇 **「建立資源** > **檢視所有**」。

    > [!div class="mx-imgBorder"]
    > ![搜尋對等互連](../media/setup-seeall.png)

1. 在搜尋框中搜尋**對等,** 然後選擇鍵盤上的 **「輸入**」 。 從結果中選擇**對等資源**。

    > [!div class="mx-imgBorder"]
    > ![啟動對等互連](../media/setup-launch.png)

1. **對等互連**開始后,請查看頁面以瞭解詳細資訊。 準備就緒后,選擇 **"創建**"。

    > [!div class="mx-imgBorder"]
    > ![建立對等互連](../media/setup-create.png)

1. 在「**創建對等」** 頁上,「**基本」** 選項卡上填寫此處所示的框。

    > [!div class="mx-imgBorder"]
    > ![對等基礎知識選項卡](../media/setup-basics-tab.png)

    * 選擇 Azure**訂閱**。
    * 對於**資源組**,可以從下拉清單中選擇現有資源組,也可以通過選擇 **"創建新**"來創建新組。 我們將為此示例創建新的資源組。
    * **名稱**對應於資源名稱,可以是您選擇的任何名稱。
    * 如果選擇現有資源群組,則自動選擇**區域**。 如果選擇創建新資源組,還需要選擇要資源駐留的 Azure 區域。

        > [!NOTE]
        > 資源組所在的區域與要與 Microsoft 創建對等互連的位置無關。 但是,最佳做法是在駐留在最近的 Azure 區域的資源組中組織對等資源。 例如,對於 Ashburn 中的對等互連,可以在美國東部或 US2 東部創建資源組。

    * 在 **『ASN』框中選擇 ASN。**

        > [!IMPORTANT]
        > * 在提交對等請求之前,您只能選擇具有"已批准狀態"的 ASN。 如果您剛剛提交了 PeerAsn 請求,請等待 12 小時左右,等待 ASN 關聯獲得批准。 如果您選擇的 ASN 正在等待驗證,您將看到一條錯誤消息。 
        > * 如果您沒有看到需要選擇的 ASN,請檢查是否選擇了正確的訂閱。 如果是這樣,請檢查是否已通過使用[對等方 ASN 創建](../howto-subscription-association-portal.md)對等 Asn 到 Azure 訂閱。

        > [!div class="mx-imgBorder"]
        > ![填寫的對等基礎知識](../media/setup-direct-basics-filled-tab.png)

    * 選擇 **「下一步 :配置>** 以繼續。
