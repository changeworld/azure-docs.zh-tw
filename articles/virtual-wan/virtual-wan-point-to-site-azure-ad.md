---
title: 設定 Azure 點對站連線的 Azure AD 驗證 | Microsoft Docs
description: 在本教學課程中，您將了解如何設定使用者 VPN 的 Azure Active Directory 驗證。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: alzam
ms.openlocfilehash: b3508c4c8da5b4987fb5f38cf3bf701f2dda1097
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122023"
---
# <a name="tutorial-create-a-user-vpn-connection-by-using-azure-virtual-wan"></a>教學課程：使用 Azure 虛擬 WAN 建立使用者 VPN 連線

本教學課程說明如何在虛擬 WAN 中設定使用者 VPN 的 Azure AD 驗證，以透過 OpenVPN VPN 連線來連線至您在 Azure 中的資源。 Azure Active Directory 驗證僅適用於使用 OpenVPN 通訊協定的閘道以及執行 Windows 的用戶端。

此類型的連線需要在用戶端電腦上設定用戶端。 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 WAN
> * 建立中樞
> * 建立 P2S 設定
> * 下載 VPN 用戶端設定檔
> * 將 P2S 設定套用到中樞
> * 將 VNet 連線至中樞
> * 下載並套用 VPN 用戶端設定
> * 檢視您的虛擬 WAN
> * 檢視資源健康情況

![虛擬 WAN 的圖表](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 您有一個要連線的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../virtual-network/quick-create-portal.md)。

* 您的虛擬網路沒有任何虛擬網路閘道。 如果您的虛擬網路有閘道 (VPN 或 ExpressRoute)，則必須移除所有閘道。 此設定需要將虛擬網路改為連線到虛擬 WAN 中樞閘道。

* 取得中樞區域的 IP 位址範圍。 中樞是虛擬 WAN 建立和使用的虛擬網路。 您為中樞區域指定的位址範圍不能與任何連線的現有虛擬網路重疊。 也不能與連線至內部部署的位址範圍重疊。 如果您不熟悉位於內部部署網路設定的 IP 位址範圍，請與能夠提供那些詳細資料的人員協調。

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="wan"></a>建立虛擬 WAN

透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。

1. 瀏覽至 [虛擬 WAN] 頁面。 在入口網站中，按一下 [+建立資源]  。 在搜尋方塊中鍵入**虛擬 WAN** 並選取 [輸入]。
2. 從結果中選取 [虛擬 WAN]  。 在 [虛擬 WAN] 頁面中，按一下 [建立]  ，以開啟 [建立 WAN] 頁面。
3. 在 [建立 WAN]  頁面的 [基本]  索引標籤中，填寫下列欄位：

   ![虛擬 WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **訂用帳戶** - 選取您要使用的訂用帳戶。
   * **資源群組** - 建立新的或使用現有的資源群組。
   * **資源群組位置** - 從下拉式清單中選擇資源位置。 WAN 是全域資源，並不會在特定區域存留。 不過，您必須選取一個區域以方便管理，以及放置所建立的 WAN 資源。
   * **名稱** - 鍵入要用來稱呼 WAN 的名稱。
   * **類型：** 標準。 如果您建立基本 WAN，則只能建立基本中樞。 基本中樞只能進行 VPN 站對站連線。
4. 填寫完欄位之後，選取 [檢閱 + 建立]  。
5. 驗證通過後，選取 [建立]  ，以建立虛擬 WAN。

## <a name="site"></a>建立空的虛擬中樞

1. 在您的虛擬 WAN 底下選取 [中樞]，然後按一下 [+新增中樞]  。

   ![新網站](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. 在 [建立虛擬中樞] 頁面上，填入下列欄位。

   **區域** - 選取您要在其中部署虛擬中樞的區域。

   **名稱** - 輸入要用來稱呼虛擬中樞的名稱。

   **中樞私人位址空間** - 採用 CIDR 標記法的中樞位址範圍。

   ![新網站](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. 按一下 [檢閱 + 建立]  。
4. 在 [驗證成功]  頁面上，按一下 [建立]  。

## <a name="site"></a>建立新的 P2S 設定

P2S 設定會定義用於連線遠端用戶端的參數。

1. 在您的虛擬 WAN 底下，選取 [使用者 VPN 設定]  。

   ![新增設定](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. 按一下 [+ 建立使用者 VPN 設定]  。

   ![新增設定](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. 輸入資訊，然後按一下 [建立] 

   ![新增設定](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="hub"></a>編輯中樞指派

1. 瀏覽至虛擬 WAN 底下的 [中樞]  刀鋒視窗。
2. 選取要與 VPN 伺服器設定產生關聯的中樞，然後按一下省略符號 (...)。

   ![新網站](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. 按一下 [編輯虛擬中樞]  。
4. 勾選 [包含點對站閘道]  核取方塊，然後挑選您想要的 [閘道縮放單位]  。

   ![新網站](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. 輸入要從中將 IP 位址指派給 VPN 用戶端的 [位址集區]  。
6. 按一下 [確認]  。
7. 此作業最多可能需要 30 分鐘的時間才能完成。

## <a name="device"></a>下載 VPN 設定檔

使用 VPN 設定檔來設定用戶端。

1. 在虛擬 WAN 的頁面上，按一下 [使用者 VPN 設定]  。
2. 在頁面頂端，按一下 [下載使用者 VPN 設定]  。
3. 檔案建立完成之後，您可以按一下連結來下載。
4. 使用設定檔檔案來設定 VPN 用戶端。

## <a name="configure-user-vpn-clients"></a>設定使用者 VPN 用戶端

若要連線，您必須下載 Azure VPN Client，並匯入在先前的步驟中在要連線至 VNet 的每一部電腦上下載的 VPN 用戶端設定檔。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>

#### <a name="to-download-the-azure-vpn-client"></a>下載 Azure VPN Client

使用此[連結](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)下載 Azure VPN Client。

#### <a name="import"></a>匯入用戶端設定檔

1. 在頁面上，選取 [匯入]  。

    ![入口](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. 瀏覽至設定檔 xml 檔案，並加以選取。 選取檔案後，請選取 [開啟]  。

    ![入口](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. 指定設定檔的名稱，然後選取 [儲存]  。

    ![入口](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. 選取 [連線]  以連線至 VPN。

    ![入口](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. 連線之後，圖示將會變成綠色，並顯示為 [已連線]  。

    ![入口](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>刪除用戶端設定檔

1. 選取要刪除的用戶端設定檔旁的省略符號 (...)。 然後，選取 [移除]  。

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. 選取 [移除]  以刪除。

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>診斷連線問題

1. 若要診斷連線問題，您可以使用**診斷**工具。 選取要診斷的 VPN 連線旁的省略符號 (...)，以顯示功能表。 然後，選取 [診斷]  。

    ![診斷](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. 在 [連線屬性]  頁面上，選取 [執行診斷]  。

    ![診斷](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. 使用您的認證登入。

    ![診斷](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. 檢視診斷結果。

    ![診斷](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>檢視您的虛擬 WAN

1. 瀏覽至虛擬 WAN。
2. 在 [概觀] 頁面中，地圖上的每個點都代表著中樞。 暫留在任一點上，即可檢視中樞健康情況摘要。
3. 在 [中樞與連線] 區段中，您可以檢視中樞狀態、網站、區域、VPN 連線狀態和輸入與輸出位元組。

## <a name="viewhealth"></a>檢視資源健康狀態

1. 瀏覽至您的 WAN。
2. 在 WAN 頁面上的 [支援 + 疑難排解]  區段中，按一下 [健康情況]  並檢視您的資源。


## <a name="cleanup"></a>清除資源

您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源。 將 "myResourceGroup" 取代為您的資源群組名稱，然後執行下列 PowerShell 命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
