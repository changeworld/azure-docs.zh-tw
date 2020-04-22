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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680917"
---
1. 在「**建立對等」** 頁上,「**設定」** 選項卡上填寫如圖所示的框。

    > [!div class="mx-imgBorder"]
    > ![建立對等頁交換對等互連類型](../media/setup-exchange-conf-tab.png)

    * 對於**對等式**,選擇 **"交換**"。
    * 選擇**SKU**做**為基本免費**。
    * 選擇要設置對等互連的**地鐵**位置。

        > [!NOTE]
        > 如果您在選取**的 Metro**位置中已與 Microsoft 建立了對等互連連接,並且首次使用門戶在該位置設置對等互連,則現有的對等互連連接將列在 **「對等互連連接**」部分中,如圖所示。 Microsoft 將自動將這些對等連接轉換為 Azure 資源,以便您可以在一個位置管理這些連接以及新連接。 有關詳細資訊,請參閱[通過使用門戶將舊 Exchange 對等對等轉換為 Azure 資源](../howto-legacy-exchange-portal.md)。
        >

1. 在 **"對等互連連接**"下,選擇 **"新建**"以為要設置的每個新連接添加一條線。

    * 要配置或修改連接設置,請選擇線路的編輯按鈕。

        > [!div class="mx-imgBorder"]
        > ![[編輯] 按鈕](../media/setup-exchange-conf-tab-edit.png)

    * 要刪除列,請選擇 **...** > **刪除**。

        > [!div class="mx-imgBorder"]
        > ![刪除按鈕](../media/setup-exchange-conf-tab-delete.png)

    * 您需要提供連接的所有設置,如下所示。

         > [!div class="mx-imgBorder"]
         > ![交換對等連接頁](../media/setup-exchange-conf-tab-connection.png)

        1. 選擇需要設定連線的**對等設施**。
        1. 在**IPv4 位址**和**IPv6 位址**框中,分別輸入使用鄰居命令在 Microsoft 路由器中配置的 IPv4 和 IPv6 位址。
        1. 輸入您將在 **「最大通告的 IPv4 位址」** 和 **「最大通告的 IPv6 位址**」框中通告的 IPv4 和 IPv6 首碼的數量。
        1. 選擇 **「確定」** 以儲存連接設定。

1. 重複此步驟,在之前選擇的**Metro**中,在 Microsoft 與您的網路同地連接的任何設施中添加更多連接。

1. 添加所有必需的連接後,選擇 **「查看 + 創建**」。。

    > [!div class="mx-imgBorder"]
    > ![對等設定選項卡](../media/setup-exchange-conf-tab-final.png)

1. 請注意,門戶運行您輸入的資訊的基本驗證。 頂部的功能區顯示消息 *「正在運行最終驗證..."*

    > [!div class="mx-imgBorder"]
    > ![對等驗證選項卡](../media/setup-direct-review-tab-validation.png)

1. 消息更改為*驗證后*,驗證您的資訊。 通過選擇 **「創建**」提交請求。 要修改請求,請選擇 **「上一步」** 並重複這些步驟。

    > [!div class="mx-imgBorder"]
    > ![對等提交](../media/setup-exchange-review-tab-submit.png)

1. 提交請求後,等待部署完成。 如果部署失敗,請與[微軟對等互連](mailto:peering@microsoft.com)。 成功部署如下所示。

    > [!div class="mx-imgBorder"]
    > ![窺視成功](../media/setup-direct-success.png)
