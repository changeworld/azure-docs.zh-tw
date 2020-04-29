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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678542"
---
1. 選取 [**建立資源** > ] [**全部查看**]。

    > [!div class="mx-imgBorder"]
    > ![搜尋對等](../media/setup-seeall.png)

1. 搜尋 [搜尋] 方塊中的 [對**等互連**]，然後選取鍵盤上的**Enter 鍵**。 從結果中，選取 [對**等互連**] 資源。

    > [!div class="mx-imgBorder"]
    > ![啟動對等互連](../media/setup-launch.png)

1. 對**等互連**啟動之後，請參閱頁面以瞭解詳細資料。 當您準備好時，請選取 [**建立**]。

    > [!div class="mx-imgBorder"]
    > ![建立對等互連](../media/setup-create.png)

1. 在 [**建立對等互連**] 頁面的 [**基本**] 索引標籤上，填寫方塊，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連基本索引標籤](../media/setup-basics-tab.png)

    * 選取您的 Azure**訂**用帳戶。
    * 針對 [**資源群組**]，您可以從下拉式清單中選擇現有的資源群組，或選取 **[新建]** 來建立新的群組。 我們會為此範例建立新的資源群組。
    * **名稱**會對應至資源名稱，而且可以是您選擇的任何專案。
    * 如果您選擇現有的資源群組，則會句子**區域**。 如果您選擇建立新的資源群組，您也必須選擇您想要資源所在的 Azure 區域。

        > [!NOTE]
        > 資源群組所在的區域與您要建立與 Microsoft 的對等互連的位置無關。 但最佳做法是在位於最接近 Azure 區域的資源群組內，組織您的對等互連資源。 例如，針對阿什本中的對等互連，您可以在「美國東部」或「東部美國2」中建立資源群組。

    * 在 [**對等 ASN** ] 方塊中選取您的 ASN。

        > [!IMPORTANT]
        > * 在提交對等互連要求之前，您只能選擇 ValidationState 為已核准的 ASN。 如果您剛提交 PeerAsn 要求，請等待12小時，或讓 ASN 關聯獲得核准。 如果您選取的 ASN 正在等待驗證，您會看到一則錯誤訊息。 
        > * 如果您沒有看到您需要選擇的 ASN，請確認您選取的是正確的訂用帳戶。 若是如此，請使用[將對等 ASN 與 Azure 訂](../howto-subscription-association-portal.md)用帳戶建立關聯，檢查您是否已建立 PeerAsn。

        > [!div class="mx-imgBorder"]
        > ![對等互連基本知識已填入](../media/setup-direct-basics-filled-tab.png)

    * 選取 **[下一步]： [設定] >** 以繼續。
