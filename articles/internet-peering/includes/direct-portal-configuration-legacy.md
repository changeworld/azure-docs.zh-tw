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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81678846"
---
1. 在 [**建立對等互連** **] 頁面**的 [設定] 索引標籤上，填寫方塊，如下所示。

    > [!div class="mx-imgBorder"]
    > ![建立對等互連頁面設定索引標籤](../media/setup-direct-conf-tab.png)

    * 針對 [對**等互連類型**]，選取 [**直接**]。
    * 針對 [ **Microsoft 網路**]，選取 [ **AS8075**]。 請勿選取 [ASN 8069]。 它會保留給特殊應用程式，而且僅供[Microsoft 對等互連](mailto:peering@microsoft.com)使用。
    * 選取 [ **SKU** ] 作為 [基本] [**免費**]。 請不要選取 [Premium Free]，因為它是保留給特殊應用程式。
    * 選取您想要將對等互連轉換至 Azure 資源的**Metro**位置。 如果您在選取的**Metro**位置中有與 Microsoft 的對等互連未轉換成 Azure 資源，則這些連線會列在 [對**等互連**連線] 區段中，如下所示。 您現在可以將這些對等互連連線轉換成 Azure 資源。

        > [!div class="mx-imgBorder"]
        > ![對等互連連接清單](../media/setup-directlegacy-conf-tab.png)

1. 如果您需要更新頻寬，請選取一行的 [編輯] 按鈕來修改連線設定。

    > [!div class="mx-imgBorder"]
    > ![[編輯] 按鈕](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 如果您想要在選取的**Metro**位置新增與 Microsoft 的其他對等互連連線，**請選取 [新建]**。 如需詳細資訊，請參閱[使用入口網站建立或修改直接對等互連](../howto-direct-portal.md)。
    >

1. 選取 [檢閱 + 建立]。 請注意，入口網站會對您所輸入的資訊執行基本驗證。 頂端的功能區會顯示執行*最終驗證*的訊息 ...。

    > [!div class="mx-imgBorder"]
    > ![對等驗證索引標籤](../media/setup-direct-review-tab-validation.png)

1. 當訊息變更為 [*通過驗證*] 之後，請確認您的資訊。 選取 [**建立**] 以提交要求。 若要修改您的要求，請選取 [**上一**步] 並重複步驟。

    > [!div class="mx-imgBorder"]
    > ![對等互連提交](../media/setup-direct-review-tab-submit.png)

1. 提交要求之後，請等候部署完成。 如果部署失敗，請連絡 [Microsoft 對等互連](mailto:peering@microsoft.com)。 如這裡所示，成功的部署隨即出現。

    > [!div class="mx-imgBorder"]
    > ![對等互連成功](../media/setup-direct-success.png)
