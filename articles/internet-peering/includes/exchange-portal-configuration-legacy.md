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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678525"
---
1. 在 [ **建立對等互連** **] 頁面的 [設定** ] 索引標籤上，填寫方塊，如下所示。

    > [!div class="mx-imgBorder"]
    > ![建立對等互連頁面交換對等互連類型](../media/setup-exchange-conf-tab.png)

    * 針對對 **等互連類型**，選取 [ **Exchange**]。
    * 選取 [**基本] 免費**的**SKU** 。
    * 選取您要將對等互連至 Azure 資源的 **Metro** 位置。 如果您在所選 **Metro** 位置中，與 Microsoft 的對等互連連線未轉換成 Azure 資源，則這些連線會列在 [對 **等互連** 連線] 區段中，如下所示。 您現在可以將這些對等互連連接轉換成 Azure 資源。

        > [!div class="mx-imgBorder"]
        > ![對等連接清單](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > 您無法修改舊版對等互連連接的設定。 如果您想要在選取的 **Metro** 位置中新增與 Microsoft 的其他對等互連連線，請選取 [ **建立新**的]。 如需詳細資訊，請參閱 [使用入口網站建立或修改 Exchange 對等互連](../howto-exchange-portal.md)。
        >

1. 選取 [檢閱 + 建立]****。 請注意，入口網站會對您輸入的資訊執行基本驗證。 頂端的功能區會顯示正在執行 *最終驗證*的訊息 ...。

    > [!div class="mx-imgBorder"]
    > ![對等互連驗證索引標籤](../media/setup-direct-review-tab-validation.png)

1. 訊息變更為 *通過驗證*之後，請確認您的資訊。 選取 [ **建立**] 來提交要求。 如果您需要修改要求，請選取 [ **上一** 步] 並重複這些步驟。

    > [!div class="mx-imgBorder"]
    > ![對等互連提交](../media/setup-exchange-review-tab-submit.png)

1. 提交要求之後，請等候部署完成。 如果部署失敗，請連絡 [Microsoft 對等互連](mailto:peering@microsoft.com)。 成功的部署隨即出現，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連成功](../media/setup-direct-success.png)
