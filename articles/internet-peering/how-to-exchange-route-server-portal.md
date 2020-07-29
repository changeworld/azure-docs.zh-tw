---
title: 使用入口網站的 Exchange 合作夥伴與路由伺服器的對等互連連線
titleSuffix: Azure
description: 使用 Azure 入口網站建立或修改與路由伺服器的交換對等互連
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1eaf7413b01bceacbcbf3640bfe654fdad026672
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700656"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>在 Azure 入口網站中建立或修改與路由伺服器的 Exchange 對等互連

本文說明如何使用 Azure 入口網站，透過路由伺服器建立 Microsoft Exchange 對等互連。 此文章也會示範如何檢查資源的狀態、加以更新，或是加以刪除並取消佈建。


## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請檢閱[先決條件](prerequisites.md)與 [Exchange 對等互連逐步解說](walkthrough-exchange-all.md)。
* 如果您已經有與 Microsoft 的 Exchange 對等互連，且尚未轉換成 Azure 資源，請參閱[使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)。

## <a name="create-and-provision-an-exchange-peering"></a>建立及佈建 Exchange 對等互連

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>登入入口網站並選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>使用路由伺服器建立 Exchange 對等互連


身為網際網路交換服務提供者，您可以透過[建立對等互連]( https://go.microsoft.com/fwlink/?linkid=2129593)，建立 Exchange 對等互連要求。

1. 在 [建立對等互連] 頁面的 [基本] 索引標籤上，填寫各方塊，如下所示：

    > [!div class="mx-imgBorder"] 
    > ![註冊對等互連服務](./media/setup-basics-tab.png)

* 選取您的 Azure 訂用帳戶。

* 針對 [資源群組]，您可以從下拉式清單中選擇現有的資源群組，或選取 [新建] 來建立新的群組。 我們將在此範例中建立新的資源群組。

* [名稱] 對應至資源名稱，而且可以是您選擇的任何名稱。

* 如果您選擇現有的資源群組，則會自動選取 [區域]。 如果您選擇建立新的資源群組，您也必須選擇您想要資源在哪個 Azure 區域。

    >[!NOTE]
    >資源群組所在的區域與您要與 Microsoft 建立對等互連的位置無關。 但最佳做法是在位於最近 Azure 區域的資源群組內，組織您的對等互連資源。 例如，在阿什本中的對等互連，可以在「美國東部」或「東部美國 2」中建立資源群組。

* 在 [PeerASN] 方塊中選取您的 ASN。

    >[!IMPORTANT] 
    >在提交對等互連要求之前，您只能選擇 ValidationState 為 [已核准] 的 ASN。 如果您剛提交 PeerAsn 要求，需等待約 12 小時讓 ASN 關聯獲得核准。 如果您選取的 ASN 正在等待驗證，您會看到錯誤訊息。 如果您沒有看到您需要選擇的 ASN，請確認您選取的是正確的訂用帳戶。 若選擇無誤，請檢查您是否已使用 **[將對等 ASN 與 Azure 訂用帳戶建立關聯](https://go.microsoft.com/fwlink/?linkid=2129592)** 建立 PeerASN。

* 完成時，選取 [下一步:**設定]** 以繼續。

#### <a name="configure-connections-and-submit"></a>設定連線並提交

1. 在 [建立對等互連] 頁面的 [設定] 索引標籤上，填寫方塊，如下所示：

    > [!div class="mx-imgBorder"]
    > ![設定路由伺服器](./media/setup-exchange-conf-tab-routeserver.png)
 
    * 針對 [對等互連類型]，選取 [**直接**]
    * 針對 [Microsoft 網路]，選取 [ **AS8075 與 exchange 路由伺服器**]。 
    * 選取 [SKU] 作為 [基本] [**免費**]。 請不要選取 [premium free]，因為它已保留給特殊應用程式。
    * 選取您想要設定對等互連的**Metro**位置。

1. 在 [對**等連接**] 底下 **，選取 [新建]**

1.  在 [**直接對等互連**連線] 底下，填入下列 BGP 會話詳細資料：

    > [!div class="mx-imgBorder"]
    > ![設定直接對等路由伺服器](./media/setup-exchange-conf-tab-direct-route.png)


     * 對等互連設備，選取適當的實體位置以進行對等互連
     * 會話位址提供者，選取對等
     * 會話 IPv4 首碼將由 exchange 提供者對等體提供
     * 點對點工作階段 IPv4 位址，會由路由伺服器的 exchange 對等體從其 IP 首碼範圍選取。
     * Microsoft 會話 IPv4 位址，將是從 IP 首碼範圍配置的路由器 IP。
     * 會話 IPv6 此時是選擇性的。
     * 已公告的 IPv4 首碼上限可達20000。 
     * 預設會停用對等互連服務的使用。 一旦 exchange 提供者已簽署與 Microsoft 的對等互連服務合約，即可啟用此功能。

1. 完成時，按一下 [**儲存**]。 

1. 在 [建立對等互連] 底下，您會看到通過驗證。 通過驗證之後，按一下 [**建立**]

    > [!div class="mx-imgBorder"]
    > ![設定的驗證](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >對於身為 Microsoft 對等服務合作夥伴的一般網際網路服務提供者（ISP），需要註冊客戶 IP 首碼。 不過，在具有路由伺服器的 exchange 合作夥伴案例中，必須註冊客戶 Asn 而不是首碼。 相同的 ASN 金鑰適用于客戶的前置詞註冊。

1. 在 [設定] 區段下選取 [**已註冊的 asn** ]。

    > [!div class="mx-imgBorder"]
    > ![設定直接對等路由伺服器](./media/setup-exchange-registered-asn.png)

1. 選取 [**新增已註冊的 asn** ]，在您的 exchange 訂閱下建立新的客戶 ASN。

    > [!div class="mx-imgBorder"]
    > ![設定直接對等路由伺服器](./media/setup-exchange-register-new-asn.png)

1. 在 [註冊 ASN] 底下，選取名稱、填入客戶 ASN，然後按一下 [儲存]。

1. 在 [已註冊] Asn 下，會有一個相關聯的首碼金鑰指派給每個 ASN。 作為交換提供者，您必須為客戶提供此首碼金鑰，讓他們可以在其訂用帳戶下註冊對等服務。

    > [!div class="mx-imgBorder"]
    > ![設定直接對等路由伺服器](./media/setup-exchange-register-asn-prefixkey.png)




### <a name="verify-an-exchange-peering"></a><a name=get></a>驗證 Exchange 對等互連
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>修改 Exchange 對等互連
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>取消佈建 Exchange 對等互連
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)
* [使用入口網站將舊版直接對等互連轉換成 Azure 資源](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請參閱[網際網路對等互連常見問題集](faqs.md)。
