---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83846113"
---
1. 選取 [建立資源]  >  [檢視全部]。

    > [!div class="mx-imgBorder"]
    > ![搜尋對等互連](../media/setup-seeall.png)

1. 在搜尋方塊中搜尋**對等互連**，然後按鍵盤上的 **Enter**。 從結果中選取一個**對等互連**的資源。

    > [!div class="mx-imgBorder"]
    > ![啟動對等互連](../media/setup-launch.png)

1. 開始對等互連之後，請檢閱頁面以瞭解詳細資料。 當您準備好時，選取 [建立]。

    > [!div class="mx-imgBorder"]
    > ![建立對等互連](../media/setup-create.png)

1. 在 [建立對等互連] 頁面的 [基本] 索引標籤上，填寫各方塊，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連基本資訊索引標籤](../media/setup-basics-tab.png)

    * 選取您的 Azure [訂用帳戶]。
    * 針對 [資源群組]，您可以從下拉式清單中選擇現有的資源群組，或選取 [新建] 來建立新的群組。 我們將在此範例中建立新的資源群組。
    * [名稱] 對應至資源名稱，而且可以是您選擇的任何名稱。
    * 如果您選擇現有的資源群組，則會自動選取 [區域]。 如果您選擇建立新的資源群組，您也必須選擇您想要資源在哪個 Azure 區域。

        > [!NOTE]
        > 資源群組所在的區域與您要與 Microsoft 建立對等互連的位置無關。 但最佳做法是在位於最近 Azure 區域的資源群組內，組織您的對等互連資源。 例如，在阿什本中的對等互連，可以在「美國東部」或「東部美國 2」中建立資源群組。

    * 在 [PeerASN] 方塊中選取您的 ASN。

        > [!IMPORTANT]
        > * 在提交對等互連要求之前，您只能選擇 ValidationState 為 [已核准] 的 ASN。 如果您剛提交 PeerAsn 要求，需等待約 12 小時讓 ASN 關聯獲得核准。 如果您選取的 ASN 正在等待驗證，您會看到錯誤訊息。 
        > * 如果您沒有看到您需要選擇的 ASN，請確認您選取的是正確的訂用帳戶。 若選擇無誤，請檢查您是否已使用[將對等 ASN 與 Azure 訂用帳戶建立關聯](../howto-subscription-association-portal.md)建立 PeerASN。

        > [!div class="mx-imgBorder"]
        > ![填寫對等互連基本資訊](../media/setup-direct-basics-filled-tab.png)

    * 選取 [下一步：**設定 >]** 以繼續。
