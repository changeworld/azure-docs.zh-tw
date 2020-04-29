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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681039"
---
1. 在 [**建立對等互連** **] 頁面**的 [設定] 索引標籤上，填寫方塊，如下所示。

    * 針對 [對**等互連類型**]，選取 [**直接**]。
    * 針對 [ **Microsoft 網路**]，選取 [ **AS8075**]。 請勿使用 ASN 8069 建立對等互連。 它會保留給特殊應用程式，而且僅供[Microsoft 對等互連](mailto:peering@microsoft.com)使用。
    * 選取 [ **SKU** ] 作為 [基本] [**免費**]。 請不要選取 [Premium Free]，因為它是保留給特殊應用程式。
    * 選取您要設定對等互連的**Metro**位置。

        > [!NOTE]
        > 如果您在選取的**Metro**位置已經有與 Microsoft 的對等互連連線，而且您第一次使用 Azure 入口網站來設定該位置的對等互連，您現有的對等互連連接會列在 [對**等**連線] 區段中，如下所示。 Microsoft 會自動將這些對等互連連線轉換成 Azure 資源，讓您可以在同一個位置管理所有連線，以及新的連接。 如需詳細資訊，請參閱[使用入口網站將舊版直接對等互連轉換成 Azure 資源](../howto-legacy-direct-portal.md)。
        >

1. 在 [對**等**連線] 底下，選取 [**建立新**的]，為您要設定的每個新連線新增一行。

    * 若要設定或修改連接設定，請選取一行的 [編輯] 按鈕。

        > [!div class="mx-imgBorder"]
        > ![[編輯] 按鈕](../media/setup-direct-conf-tab-edit.png)
    
    * 若要刪除一行，請選取 [ **...** ] > **刪除**。

        > [!div class="mx-imgBorder"]
        > ![刪除按鈕](../media/setup-direct-conf-tab-delete.png)

    * 您必須提供連線的所有設定，如下所示。

         > [!div class="mx-imgBorder"]
         > ![直接對等互連連接頁面](../media/setup-direct-conf-tab-connection.png)

        1. 選取需要設定連接的對**等互連設施**。
        1. **會話位址提供者**是用來判斷在您的網路與 Microsoft 之間設定 BGP 會話所需的子網。 如果您可以提供子網，請選取 [**對等**]。 否則，請選取 [ **microsoft**和[microsoft 對等互連](mailto:peering@microsoft.com)] 來與您聯絡。 選擇此選項需要較長的時間，讓 Microsoft 處理對等互連要求。 在某些情況下，Microsoft 可能無法提供子網，這會導致要求拒絕。
        1. 如果您已選取 [**會話位址提供者**] 選項作為 [**對等**]，請分別在 [**會話 Ipv4 首碼**] 和 [**會話 IPv6 首碼**] 方塊中輸入 IPv4 和 IPv6 位址以及首碼遮罩。
        1. 在 [**公告的 ipv4 位址上限**] 和 [已**公告的 ipv6 位址上限**] 方塊中，輸入您要公告的 ipv4 和 IPv6 首碼數目。
        1. 調整 [**總頻寬**] 滑杆以反映連線的頻寬。
        1. 選取 [**儲存**] 以儲存您的連線設定。

1. 重複上一個步驟，在您先前選取的**Metro**中，于 Microsoft 與您的網路共存的任何設備上新增更多連線。

1. 新增所有必要的連線之後，請選取 [**審查 + 建立**]。

    > [!div class="mx-imgBorder"]
    > ![對等互連設定索引標籤最終](../media/setup-direct-conf-tab-final.png)

1. 請注意，入口網站會對您所輸入的資訊執行基本驗證。 頂端的功能區會顯示執行*最終驗證*的訊息 ...。

    > [!div class="mx-imgBorder"]
    > ![對等驗證索引標籤](../media/setup-direct-review-tab-validation.png)

1. 當訊息變更為 [*通過驗證*] 之後，請確認您的資訊。 選取 [**建立**] 以提交要求。 若要修改您的要求，請選取 [**上一**步] 並重複步驟。

    > [!div class="mx-imgBorder"]
    > ![對等互連提交](../media/setup-direct-review-tab-submit.png)

1. 提交要求之後，等待部署完成。 如果部署失敗，請聯絡[Microsoft 對等互連](mailto:peering@microsoft.com)。 如這裡所示，成功的部署隨即出現。

    > [!div class="mx-imgBorder"]
    > ![對等互連成功](../media/setup-direct-success.png)
