---
title: 使用 Azure 入口網站站建立或修改直接對等互連
titleSuffix: Azure
description: 使用 Azure 入口網站站建立或修改直接對等互連
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: e49462612b58163c2ac51b78584761d0d8b8bd06
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700560"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>使用 Azure 入口網站站建立或修改直接對等互連

本文說明如何使用 Azure 入口網站，為網際網路服務提供者或網際網路交換服務提供者提供者建立 Microsoft 直接對等互連。 本文也會示範如何檢查資源的狀態、加以更新、或是加以刪除並取消佈建。

如果您想要的話，可以使用 Azure [PowerShell](howto-direct-powershell.md) 完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請閱讀[必要條件](prerequisites.md)和[直接對等互連逐步解說](walkthrough-direct-all.md)。
* 如果您已有與 Microsoft 的直接對等互連連線，且尚未轉換成 Azure 資源，請參閱[使用入口網站將舊版直接對等互連轉換成 Azure 資源](howto-legacy-direct-portal.md)。

## <a name="create-and-provision-a-direct-peering"></a>建立和佈建直接對等互連

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>登入入口網站並選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>建立直接對等互連

身為網際網路服務提供者或網際網路交換服務提供者，您可以[建立對等互連]( https://go.microsoft.com/fwlink/?linkid=2129593)的方式建立新的直接對等互連要求。

1. 在 [建立對等互連] 頁面的 [基本] 索引標籤上，填寫各方塊，如下所示：


    ![註冊對等互連服務](./media/setup-basics-tab.png)

2. 選取您的 Azure 訂用帳戶。

3. 針對 [資源群組]，您可以從下拉式清單中選擇現有的資源群組，或選取 [新建] 來建立新的群組。 我們將在此範例中建立新的資源群組。

4. [名稱] 對應至資源名稱，而且可以是您選擇的任何名稱。

5. 如果您選擇現有的資源群組，則會自動選取 [區域]。 如果您選擇建立新的資源群組，您也必須選擇您想要資源在哪個 Azure 區域。

    >[!NOTE]
    > 資源群組所在的區域與您要與 Microsoft 建立對等互連的位置無關。 但最佳做法是在位於最近 Azure 區域的資源群組內，組織您的對等互連資源。 例如，在阿什本中的對等互連，可以在「美國東部」或「東部美國 2」中建立資源群組。

6. 在 [PeerASN] 方塊中選取您的 ASN。

    >[!IMPORTANT]
    >在提交對等互連要求之前，您只能選擇 ValidationState 為 [已核准] 的 ASN。 如果您剛提交 PeerAsn 要求，需等待約 12 小時讓 ASN 關聯獲得核准。 如果您選取的 ASN 正在等待驗證，您會看到錯誤訊息。 如果您沒有看到您需要選擇的 ASN，請確認您選取的是正確的訂用帳戶。 若選擇無誤，請檢查您是否已使用 **[將對等 ASN 與 Azure 訂用帳戶建立關聯](https://go.microsoft.com/fwlink/?linkid=2129592)** 建立 PeerASN。

7. 完成時，選取 [下一步:**設定]** 以繼續。



    ![註冊對等互連服務](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>設定連線並提交
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>驗證直接對等互連
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>修改直接對等互連
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>取消佈建直接對等互連
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改交換服務對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版交換服務對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請參閱[網際網路對等互連常見問題集](faqs.md)。
