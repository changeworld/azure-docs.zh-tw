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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680917"
---
1. 在 [**建立對等互連** **] 頁面**的 [設定] 索引標籤上，填寫方塊，如下所示。

    > [!div class="mx-imgBorder"]
    > ![建立對等互連頁面交換對等互連類型](../media/setup-exchange-conf-tab.png)

    * 針對 [對**等互連類型**]，選取 [ **Exchange**]。
    * 選取 [ **SKU** ] 作為 [基本] [**免費**]。
    * 選取您要設定對等互連的**Metro**位置。

        > [!NOTE]
        > 如果您在選取的**Metro**位置已經有與 Microsoft 的對等互連連線，而且第一次使用入口網站來設定該位置的對等互連，您現有的對等互連連線會列在 [對**等**連線] 區段中，如下所示。 Microsoft 會自動將這些對等互連連線轉換成 Azure 資源，讓您可以在一處管理所有連線，以及新的連線。 如需詳細資訊，請參閱[使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](../howto-legacy-exchange-portal.md)。
        >

1. 在 [對**等**連線] 底下，選取 [**建立新**的]，為您要設定的每個新連線新增一行。

    * 若要設定或修改連接設定，請選取一行的 [編輯] 按鈕。

        > [!div class="mx-imgBorder"]
        > ![[編輯] 按鈕](../media/setup-exchange-conf-tab-edit.png)

    * 若要刪除一行，請選取 [ **...** ] > **刪除**。

        > [!div class="mx-imgBorder"]
        > ![刪除按鈕](../media/setup-exchange-conf-tab-delete.png)

    * 您必須提供連線的所有設定，如下所示。

         > [!div class="mx-imgBorder"]
         > ![Exchange 對等互連連接頁面](../media/setup-exchange-conf-tab-connection.png)

        1. 選取需要設定連接的對**等互連設施**。
        1. 在 [ **ipv4 位址**] 和 [ **ipv6 位址**] 方塊中，分別輸入 ipv4 和 ipv6 位址，這會在 Microsoft 路由器中使用 [鄰近] 命令來設定。
        1. 在 [**公告的 ipv4 位址上限**] 和 [已**公告的 ipv6 位址上限**] 方塊中，輸入您要公告的 ipv4 和 IPv6 首碼數目。
        1. 選取 **[確定]** 以儲存您的連線設定。

1. 重複此步驟，在 Microsoft 與您的網路共存的任何設備上，于先前所選的**Metro**中新增更多連線。

1. 新增所有必要的連線之後，請選取 [**審查 + 建立**]。

    > [!div class="mx-imgBorder"]
    > ![對等設定索引標籤](../media/setup-exchange-conf-tab-final.png)

1. 請注意，入口網站會對您所輸入的資訊執行基本驗證。 頂端的功能區會顯示執行*最終驗證*的訊息 ...。

    > [!div class="mx-imgBorder"]
    > ![對等驗證索引標籤](../media/setup-direct-review-tab-validation.png)

1. 當訊息變更為 [*通過驗證*] 之後，請確認您的資訊。 選取 [**建立**] 以提交要求。 若要修改您的要求，請選取 [**上一**步] 並重複步驟。

    > [!div class="mx-imgBorder"]
    > ![對等互連提交](../media/setup-exchange-review-tab-submit.png)

1. 提交要求之後，請等候部署完成。 如果部署失敗，請聯絡[Microsoft 對等互連](mailto:peering@microsoft.com)。 如這裡所示，成功的部署隨即出現。

    > [!div class="mx-imgBorder"]
    > ![對等互連成功](../media/setup-direct-success.png)
