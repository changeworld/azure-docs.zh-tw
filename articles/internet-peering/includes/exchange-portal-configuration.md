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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680917"
---
1. 在 [ **建立對等互連** **] 頁面的 [設定** ] 索引標籤上，填寫方塊，如下所示。

    > [!div class="mx-imgBorder"]
    > ![建立對等互連頁面交換對等互連類型](../media/setup-exchange-conf-tab.png)

    * 針對對 **等互連類型**，選取 [ **Exchange**]。
    * 選取 [**基本] 免費**的**SKU** 。
    * 選取您要設定對等互連的 **Metro** 位置。

        > [!NOTE]
        > 如果您在所選 **Metro** 位置已有與 Microsoft 的對等互連連線，而且您第一次使用入口網站來設定該位置的對等互連，您現有的對等互連連線會列在 [對 **等互連** 連線] 區段中，如下所示。 Microsoft 會自動將這些對等互連連線轉換成 Azure 資源，如此一來，您就可以在單一位置管理這些連線和新的連接。 如需詳細資訊，請參閱 [使用入口網站將舊版 Exchange 對等互連轉換為 Azure 資源](../howto-legacy-exchange-portal.md)。
        >

1. 在 [對 **等互連**連線] 下，選取 [ **建立新** 的]，為您想要設定的每個新連接新增一行。

    * 若要設定或修改連接設定，請選取線條的 [編輯] 按鈕。

        > [!div class="mx-imgBorder"]
        > ![編輯按鈕](../media/setup-exchange-conf-tab-edit.png)

    * 若要刪除一行，請選取 **...**  > **Delete**。

        > [!div class="mx-imgBorder"]
        > ![刪除按鈕](../media/setup-exchange-conf-tab-delete.png)

    * 您必須提供連線的所有設定，如下所示。

         > [!div class="mx-imgBorder"]
         > ![Exchange 對等互連連接頁面](../media/setup-exchange-conf-tab-connection.png)

        1. 選取必須設定連接的對 **等互連設備** 。
        1. 在 [ **ipv4 位址** ] 和 [ **ipv6 位址** ] 方塊中，分別輸入 ipv4 和 ipv6 位址，這會使用 [鄰居] 命令在 Microsoft 路由器中進行設定。
        1. 在 [ **通告的 ipv4 位址** 和 **最大通告 IPv6 位址** ] 方塊中，輸入您將通告的 ipv4 和 IPv6 首碼數目。
        1. 選取 **[確定]** 以儲存您的連接設定。

1. 重複此步驟，在先前選取的 **Metro** 內，于 Microsoft 與您網路共置的任何設備上新增更多連接。

1. 新增所有必要的連接之後，請選取 [ **審核 + 建立**]。

    > [!div class="mx-imgBorder"]
    > ![對等互連設定索引標籤](../media/setup-exchange-conf-tab-final.png)

1. 請注意，入口網站會對您輸入的資訊執行基本驗證。 頂端的功能區會顯示正在執行 *最終驗證*的訊息 ...。

    > [!div class="mx-imgBorder"]
    > ![對等互連驗證索引標籤](../media/setup-direct-review-tab-validation.png)

1. 訊息變更為 *通過驗證*之後，請確認您的資訊。 選取 [ **建立**] 來提交要求。 若要修改您的要求，請選取 [ **上一** 步] 並重複這些步驟。

    > [!div class="mx-imgBorder"]
    > ![對等互連提交](../media/setup-exchange-review-tab-submit.png)

1. 提交要求之後，請等候部署完成。 如果部署失敗，請連絡 [Microsoft 對等互連](mailto:peering@microsoft.com)。 成功的部署隨即出現，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連成功](../media/setup-direct-success.png)
