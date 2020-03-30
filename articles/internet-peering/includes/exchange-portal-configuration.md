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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774493"
---
1. 在"**創建對等"** 頁上，**在"配置"** 選項卡下填寫欄位，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等配置 - 交換](../media/setup-exchange-conf-tab.png)

    * 對於**對等式**，選擇 *"交換*"。
    * 選擇**SKU**作為*基本免費*。
    * 選擇要設置對等互連的 **"地鐵**"位置。

        > [!NOTE]
        > 如果您在所選**的 Metro**位置中已與 Microsoft 建立了對等互連連接，並且首次使用門戶在該位置設置對等互連，則現有的對等互連連接將列在 **"對等互連連接**"部分，如下所示。 Microsoft 將自動將這些對等連接轉換為 Azure 資源，以便您可以在一個位置管理這些連接以及新連接。有關詳細資訊[，請參閱使用門戶將舊版 Exchange 對等互連轉換為 Azure 資源](../howto-legacy-exchange-portal.md)。
        >

1. 在 **"對等互連連接**"下，按一下 **"創建新**"以為要設置的每個新連接添加一行。

    * 要配置/修改連接設置，請按一下行的編輯按鈕。

        > [!div class="mx-imgBorder"]
        > ![對等配置 - 交換編輯](../media/setup-exchange-conf-tab-edit.png)

    * 要刪除一行，請按一下 **...** 按鈕>**刪除**。

        > [!div class="mx-imgBorder"]
        > ![對等配置 - 交換編輯](../media/setup-exchange-conf-tab-delete.png)

    * 您需要提供連接的所有設置，如下所示。

         > [!div class="mx-imgBorder"]
         > ![對等配置 - 交換連接](../media/setup-exchange-conf-tab-connection.png)

        1. 選擇需要設置連接的**對等設施**。
        1. 在欄位中**IPv4 位址**和**IPv6 位址**中，分別輸入 IPv4 和 IPv6 位址，這些位址將使用鄰居命令在 Microsoft 路由器中配置。
        1. 輸入您將在欄位中通告的 IPv4 和 IPv6 首碼的數量 **"最大播發的 IPv4 位址**"和 **"最大通告的 IPv6 位址**"。
        1. 按一下"**確定"** 以保存連接設置。

1. 重複上述步驟，在之前選擇的**Metro**中，在 Microsoft 與您的網路同地連接的任何設施中添加更多連接。

1. 添加所有必需的連接後，按一下"**審閱 + 創建**"。

    > [!div class="mx-imgBorder"]
    > ![對等配置選項卡最終](../media/setup-exchange-conf-tab-final.png)

1. 請注意，門戶運行您輸入的資訊的基本驗證。 這顯示在頂部的功能區中，如*正在運行最終驗證...*

    > [!div class="mx-imgBorder"]
    > ![對等驗證選項卡](../media/setup-direct-review-tab-validation.png)

1. 在它變為*驗證通過*後，驗證您的資訊，並通過按一下"**創建**"提交請求。 如果需要修改請求，請按一下 **"上一步"** 並重複上述步驟。

    > [!div class="mx-imgBorder"]
    > ![對等提交](../media/setup-exchange-review-tab-submit.png)

1. 提交請求後，請等待它完成部署。 如果部署失敗，請與[微軟對等互連](mailto:peering@microsoft.com)。 成功部署將顯示如下。

    > [!div class="mx-imgBorder"]
    > ![窺視成功](../media/setup-direct-success.png)
