---
title: 使用入口網站將對等 ASN 與 Azure 訂用帳戶互連
titleSuffix: Azure
description: 使用入口網站將對等 ASN 與 Azure 訂用帳戶互連
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/18/2020
ms.author: derekol
ms.openlocfilehash: 22cb179925f95fd0762371cc904fcbd02675339a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540263"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>使用入口網站將對等 ASN 與 Azure 訂用帳戶互連

作為網際網路服務提供者或網際網路交換服務提供者，您應該先使用下列步驟將您的 ASN 與 Azure 訂用帳戶產生關聯，然後再提交對等互連要求。

如果您想要的話，可以使用 [PowerShell](howto-subscription-association-powershell.md) 完成本指南。

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>建立 PeerAsn 以將您的 ASN 與 Azure 訂用帳戶建立關聯

### <a name="sign-in-to-the-portal"></a>登入入口網站
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>註冊對等互連資源提供者
依照下列步驟，註冊您訂用帳戶中的對等互連資源提供者。 如果您未執行此動作，則無法存取設定對等互連所需的 Azure 資源。

1. 按一下入口網站左上角的 [訂用帳戶]。 如果您沒看見這個項目，請按一下 [更多服務]，並搜尋該項目。

    > [!div class="mx-imgBorder"]
    > ![開啟訂用帳戶](./media/rp-subscriptions-open.png)

1. 按一下您要用於對等互連的訂用帳戶。

    > [!div class="mx-imgBorder"]
    > ![啟動訂用帳戶](./media/rp-subscriptions-launch.png)

1. 訂用帳戶開啟後，在左側按一下 [資源提供者]。 然後，在右側窗格的搜尋視窗中，搜尋 [對等互連]，或使用捲軸來尋找 [Microsoft.Peering]，並查看 [狀態]。 如果狀態為 [已註冊]，請略過以下步驟，並繼續進行 [建立 PeerAsn] 區段。 如果狀態為 [未註冊]，請選取 [Microsoft.Peering]，然後按一下 [註冊]。

    > [!div class="mx-imgBorder"]
    > ![註冊開始](./media/rp-register-start.png)

1. 您會發現狀態變更為 [正在註冊]。

    > [!div class="mx-imgBorder"]
    > ![註冊正在進行中](./media/rp-register-progress.png)

1. 請稍後，等待完成註冊。 然後，按一下 [重新整理]，並確認狀態為 [已註冊]。

    > [!div class="mx-imgBorder"]
    > ![註冊已完成](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>建立 PeerAsn
您可以使用網際網路服務提供者或網際網路 Exchange 提供者來建立新的 PeerAsn 資源，以將自發系統編號 (ASN) 與 Azure 訂用帳戶建立關聯，其與 [對等 ASN 頁面相關聯](https://go.microsoft.com/fwlink/?linkid=2129592) 。 您可以為需要建立關聯的每個 ASN 建立 **PeerAsn** ，以將多個 asn 關聯至訂用帳戶。

1. 在 [將對等 ASN 建立關聯] 頁面的 [基本] 索引標籤下方，填寫欄位，如下所示。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 基本索引標籤](./media/peerasn-basics-tab.png)

    * **名稱**對應至資源名稱，而且可以是您選擇的任何名稱。  
    * 選擇您需要將 ASN 與其建立關聯的**訂用帳戶**。
    * **對等名稱**對應至您的公司名稱，而且必須盡量接近您的 PeeringDB 設定檔。 請注意，此值僅支援字元 a-z、A-Z 和空格
    * 在 [對等 ASN] 欄位中，輸入您的 ASN。
    * 按一下 [立即建立]，並為您的「網路作業中心」(NOC) 輸入 [電子郵件地址] 和 [電話號碼]
1. 然後，按一下 [審查 + 建立]，並觀察入口網站是否針對您輸入的資訊執行基本驗證。 這會在頂端的功能區中，顯示為 [正在執行最後驗證...]。

    > [!div class="mx-imgBorder"]
    > ![螢幕擷取畫面：顯示 [對等] 與 [S N 基本] 索引標籤的關聯。](./media/peerasn-review-tab-validation.png)

1. 當功能區中的訊息變成 [已通過驗證] 時，請按一下 [建立]，以驗證您的資訊並提交要求。 如果未通過驗證，請按一下 [上一步]，並重複上述步驟，以修改您的要求，並確定您輸入的值沒有任何錯誤。

    > [!div class="mx-imgBorder"]
    > ![螢幕擷取畫面顯示 [將對等 A] 的 [基本] 索引標籤與通過驗證相關聯。](./media/peerasn-review-tab.png)

1. 提交要求之後，請等待完成部署。 如果部署失敗，請連絡 [Microsoft 對等互連](mailto:peering@microsoft.com)。 成功的部署將如下所示。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 成功](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>PeerAsn 的檢視狀態
成功部署 PeerAsn 資源後，您需要等待 Microsoft 核准關聯要求。 核准最多可能需要 12 小時。 核准後，您會在上一節輸入的電子郵件地址中收到通知。

> [!IMPORTANT]
> 先等待 ValidationState 變成 [已核准]，然後再提交對等互連要求。 此核准最多可能需要 12 小時。

## <a name="modify-peerasn"></a>修改 PeerAsn
目前不支援修改 PeerAsn。 如果您需要修改，請連絡 [Microsoft 對等互連](mailto:peering@microsoft.com)。

## <a name="delete-peerasn"></a>刪除 PeerAsn
目前不支援刪除 PeerAsn。 如果您需要刪除 PeerAsn，請連絡 [Microsoft 對等互連](mailto:peering@microsoft.com)。

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)
* [使用入口網站將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)
* [使用入口網站建立或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請造訪[網際網路對等互連常見問題集](faqs.md)
