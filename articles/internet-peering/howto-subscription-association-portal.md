---
title: 使用入口網站將對等 ASN 與 Azure 訂用帳戶互連
titleSuffix: Azure
description: 使用入口網站將對等 ASN 與 Azure 訂用帳戶互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75912174"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>使用入口網站將對等 ASN 與 Azure 訂用帳戶互連

提交對等互連要求之前，您應該先使用下列步驟，將您的 ASN 與 Azure 訂用帳戶產生關聯。

如果您想要的話，可以使用[PowerShell](howto-subscription-association-powershell.md)來完成本指南。

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>建立 PeerAsn 以將您的 ASN 與 Azure 訂用帳戶建立關聯

### <a name="sign-in-to-the-portal"></a>登入入口網站
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>註冊對等互連資源提供者
遵循下列步驟，在您的訂用帳戶中註冊對等互連資源提供者。 如果您未執行此動作，則無法存取設定對等互連所需的 Azure 資源。

1. 按一下入口**Subscriptions**網站左上角的 [訂用帳戶]。 如果您沒有看到它，請按一下 [**更多服務**] 並搜尋它。

    > [!div class="mx-imgBorder"]
    > ![開啟訂閱](./media/rp-subscriptions-open.png)

1. 按一下您要用於對等互連的訂用帳戶。

    > [!div class="mx-imgBorder"]
    > ![啟動訂用帳戶](./media/rp-subscriptions-launch.png)

1. 訂用帳戶開啟後，在左側按一下 [**資源提供者**]。 然後在右窗格中，搜尋搜尋視窗中的 [對*等互連*]，或使用捲軸來尋找 [Microsoft] 對**等互連**，並查看**狀態**。 如果狀態為 [***已註冊***]，請略過下列步驟，並繼續進行 [**建立 PeerAsn**] 一節。 如果狀態為***NotRegistered***，請選取 [ **Microsoft 對等互連**]，然後按一下 [**註冊**]。

    > [!div class="mx-imgBorder"]
    > ![註冊開始](./media/rp-register-start.png)

1. 觀察狀態會變更為 [***註冊***]。

    > [!div class="mx-imgBorder"]
    > ![註冊進行中](./media/rp-register-progress.png)

1. 等待最小或完成註冊。 然後按一下 [重新整理] **，並確認**狀態為 [***已註冊***]。

    > [!div class="mx-imgBorder"]
    > ![註冊完成](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>建立 PeerAsn
您可以建立新的 PeerAsn 資源，將自發系統編號（ASN）與 Azure 訂用帳戶產生關聯。 您可以為需要關聯的每個 ASN 建立**PeerAsn** ，藉此建立多個 asn 與訂用帳戶的關聯。

1. 按一下 [**建立資源** > ] [**全部查看**]。

    > [!div class="mx-imgBorder"]
    > ![搜尋 PeerAsn](./media/peerasn-seeall.png)

1. 在搜尋方塊中搜尋*PeerAsn* ，然後按鍵盤上的*Enter 鍵*。 從結果中，按一下 [ **PeerAsn**資源]。

    > [!div class="mx-imgBorder"]
    > ![啟動 PeerAsn](./media/peerasn-launch.png)

1. 啟動**PeerAsn**之後，按一下 [**建立**]。

    > [!div class="mx-imgBorder"]
    > ![建立 PeerAsn](./media/peerasn-create.png)

1. 在 [**關聯對等 ASN** ] 頁面的 [**基本**] 索引標籤底下，填寫欄位，如下所示。

    > [!div class="mx-imgBorder"]
    > ![[PeerAsn 基本] 索引標籤](./media/peerasn-basics-tab.png)

    * **名稱**會對應至資源名稱，而且可以是您選擇的任何專案。  
    * 選擇您需要與 ASN 建立關聯的**訂**用帳戶。
    * **對等名稱**會對應至您公司的名稱，而且必須盡可能靠近您的 PeeringDB 設定檔。 請注意，此值僅支援字元 a-z、A-z 和空格
    * 在 [**對等 ASN** ] 欄位中輸入您的 asn。
    * 按一下 [**新建]，然後**輸入網路營運中心（NOC）的**電子郵件地址**和**電話號碼**
1. 然後，按一下 [**審核] [+ 建立**]，並觀察入口網站對您輸入的資訊執行基本驗證。 這會顯示在頂端的功能區中，如同執行*最後的驗證 ...*。

    > [!div class="mx-imgBorder"]
    > ![[PeerAsn 審核] 索引標籤](./media/peerasn-review-tab-validation.png)

1. 當功能區中的訊息變成*通過驗證*之後，請按一下 [**建立**] 來驗證您的資訊並提交要求。 如果未通過驗證，請按一下 [上**一**步]，然後重複上述步驟來修改您的要求，並確定您輸入的值沒有任何錯誤。

    > [!div class="mx-imgBorder"]
    > ![[PeerAsn 審核] 索引標籤](./media/peerasn-review-tab.png)

1. 提交要求之後，請等候它完成部署。 如果部署失敗，請聯絡[Microsoft 對等互連](mailto:peering@microsoft.com)。 成功的部署將會如下所示。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 成功](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>PeerAsn 的檢視狀態
成功部署 PeerAsn 資源之後，您將需要等候 Microsoft 核准關聯要求。 核准最多可能需要12小時的時間。 一經核准，您就會收到在上一節中輸入之電子郵件地址的通知。

> [!IMPORTANT]
> 等待 ValidationState 「核准」，然後再提交對等要求。 此核准最多可能需要12小時的時間。

## <a name="modify-peerasn"></a>修改 PeerAsn
目前不支援修改 PeerAsn。 如果您需要修改，請聯絡[Microsoft 對等互連](mailto:peering@microsoft.com)。

## <a name="delete-peerasn"></a>刪除 PeerAsn
目前不支援刪除 PeerAsn。 如果您需要刪除 PeerAsn，請聯絡[Microsoft 對等互連](mailto:peering@microsoft.com)。

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)
* [使用入口網站將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)
* [使用入口網站建立或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請造訪[網際網路對等常見問題](faqs.md)