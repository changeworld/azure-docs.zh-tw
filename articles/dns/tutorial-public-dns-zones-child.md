---
title: 教學課程：建立 Azure 子 DNS 區域
titleSuffix: Azure DNS
description: 如何在 Azure 入口網站中建立子 DNS 區域的教學課程。
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: c763f761d77ac05d9f6b45e5656436faa5697e88
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310072"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>教學課程：建立新的子系 DNS 區域

在本教學課程中，您會了解如何： 

> [!div class="checklist"]
> * 登入 Azure 入口網站。
> * 透過新的 DNS 區域建立子 DNS 區域。
> * 透過上層 DNS 區域建立子 DNS 區域。
> * 驗證新子 DNS 區域的 NS 委派。



## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。  如果您沒有帳戶，可以[在這裡免費建立](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 現有的上層 Azure DNS 區域。  

基於本教學課程的目的，我們將使用 contoso.com 作為上層區域，並使用 subdomain.contoso.com 作為子網域名稱。  以您的父網域名稱取代 contoso.com，並以您的子網域取代 subdomain。  如果您尚未建立上層 DNS 區域，請參閱[使用 Azure 入口網站建立 DNS 區域](https://docs.microsoft.com/azure/dns/dns-getstarted-portal#create-a-dns-zone)中的步驟。 


## <a name="sign-in-to-azure-portal"></a>登入 Azure 入口網站

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。

您有兩種方式可建立子 DNS 區域。
1.  透過「建立 DNS 區域」入口網站頁面。
1.  透過父 DNS 區域的組態頁面。


## <a name="create-child-dns-zone-via-create-dns-zone"></a>透過建立 DNS 區域來建立子 DNS 區域

在此步驟中，我們將建立名為 **subdomain.contoso.com** 的新子 DNS 區域，並將其委派給現有的上層 DNS 區域 **contoso.com**。 您將使用**建立 DNS 區域**頁面上的索引標籤來建立 DNS 區域。
1.  從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]。 [新增] 視窗隨即出現。
1.  選取 [網路]，然後選取 [DNS 區域] ，再選取 [新增] 按鈕。

1.  在**基本項目**索引標籤中，輸入或選取下列值：
    * 訂用帳戶：選取要在其中建立區域的訂用帳戶。
    * **資源群組**：輸入現有的資源群組，或選取 [建立新的] 並輸入 MyResourceGroup，然後選取 [確定] 來建立新的資源群組。 資源群組名稱在 Azure 訂用帳戶中必須是唯一的。
    * 勾選此核取方塊：**此區域是已裝載在 Azure DNS 中的現有區域子系**
    * **上層區域訂用帳戶**：從下拉式清單搜尋和/或選取用來建立上層 DNS 區域 contoso.com 的訂用帳戶名稱。
    * **上層區域**：在搜尋列類型中輸入 contoso.com，將其載入到下拉式清單。 載入之後，請從下拉式清單中選取 [contoso.com]。
    * **名稱：** 在此教學課程範例中，輸入 subdomain。 請注意，當我們從上一個步驟選取上層區域時，系統會將您的上層 DNS 區域名稱 contoso.com 自動新增為名稱尾碼。

1. 完成時，選取 [下一步:檢閱 + 建立]。
1. 在**檢閱 + 建立**索引標籤上檢閱摘要，並更正任何驗證錯誤，然後選取 [建立]。
建立區域可能需要幾分鐘的時間。

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="[建立 DNS 區域] 頁面的螢幕擷取畫面。" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>透過上層 DNS 區域建立子 DNS 區域的概觀頁面
您也可以從上層區域的概觀頁面使用 [子區域] 按鈕建立新的子 DNS 區域，並委派給上層 DNS 區域。 使用此按鈕會自動預先填入子區域的父參數。 

1.  在 Azure 入口網站中的 [所有資源] 下方，開啟 **MyResourceGroup** 資源群組中的 contoso.com DNS 區域。 您可以在 [依名稱篩選] 方塊中輸入 contoso.com，以輕鬆找出該區域。
1.  在 DNS 區域概觀頁面上，選取 [+子區域] 按鈕。

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="[建立 DNS 區域] 頁面的螢幕擷取畫面。" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  接著會開啟 [建立 DNS 區域] 頁面。 已核取 [子區域] 選項，且系統已在此頁面上為您填入了上層區域訂用帳戶和上層區域。
1.  在此教學課程範例中，輸入 child 作為名稱。 請注意，系統會將您的上層 DNS 區域名稱 contoso.com 自動新增為名稱的前置詞。
1.  完成時，選取 [下一步:  標籤]，然後選取 [下一步:  檢閱 + 建立]。
1.  在**檢閱 + 建立**索引標籤上檢閱摘要，並更正任何驗證錯誤，然後選取 [建立]。

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="[建立 DNS 區域] 頁面的螢幕擷取畫面。" border="true"  lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::
## <a name="verify-child-dns-zone"></a>驗證子 DNS 區域
現在您已建立了新的子 DNS 區域 subdomain.contoso.com。 若要確認委派是否正確運作，您需要檢查子區域的名稱伺服器 (NS) 記錄是否在上層區域中，如下所述。  

**擷取子 DNS 區域的名稱伺服器：**

1.  在 Azure 入口網站中的**所有資源**下方，開啟 **MyResourceGroup** 資源群組中的 subdomain.contoso.com DNS 區域。 您可以在 [依名稱篩選] 方塊中輸入 subdomain.contoso.com，以輕鬆找出該區域。
1.  從 DNS 區域概觀頁面中擷取名稱伺服器。 在此範例中，系統已為區域 contoso.com 指派名稱伺服器 ns1-08.azure-dns.com、ns2-08.azure-dns.net、ns3-08.azure-dns.org 和 ns4-08.azure-dns.info：

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="[建立 DNS 區域] 頁面的螢幕擷取畫面。" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**確認上層 DNS 區域中的 NS 記錄：**

現在在此步驟中，我們會前往上層 DNS 區域 contoso.com並檢查是否已建立子區域名稱伺服器的 NS 記錄集項目。

1. 在 Azure 入口網站中的**所有資源** 下方，開啟 MyResourceGroup 資源群組中的 contoso.com DNS 區域。 您可以在**依名稱篩選**方塊中輸入 contoso.com，以輕鬆找出該區域。
1.  在 contoso.com DNS 區域概觀頁面上，檢查是否有記錄集。
1.  您會發現，系統已在上層 DNS 區域中建立 NS 和名稱子網域類型的記錄集。 檢查此記錄集的值，其與上述步驟中從子 DNS 區域擷取的名稱伺服器清單類似。

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="[建立 DNS 區域] 頁面的螢幕擷取畫面。" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>清除資源
若不再需要在本教學課程中建立的資源，請刪除 **MyResourceGroup** 資源群組，以移除這些資源。 開啟 **MyResourceGroup** 資源群組，然後選取 [刪除資源群組]****。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure DNS 私人區域案例](private-dns-scenarios.md)
