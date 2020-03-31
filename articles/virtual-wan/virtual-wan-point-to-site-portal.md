---
title: 使用 Azure 虛擬 WAN 來與 Azure 建立點對站連線 | Microsoft Docs
description: 在本教學課程中，了解如何使用 Azure 虛擬 WAN 來與 Azure 建立點對站 VPN 連線。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: alzam
ms.openlocfilehash: fd415e1da00f52a9a3b55c946a07a30cf841cf4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060302"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>教學課程：使用 Azure 虛擬 WAN 建立使用者 VPN 連線

本教學課程會示範如何使用虛擬 WAN，透過 IPsec/IKE (IKEv2) 或 OpenVPN VPN 連線來與 Azure 中的資源連線。 此類型的連線需要在用戶端電腦上設定用戶端。 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)

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

![虛擬 WAN 的圖表](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 您有一個要連線的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../virtual-network/quick-create-portal.md)。

* 您的虛擬網路沒有任何虛擬網路閘道。 如果您的虛擬網路有閘道 (VPN 或 ExpressRoute)，則必須移除所有閘道。 此設定需要將虛擬網路改為連線到虛擬 WAN 中樞閘道。

* 取得中樞區域的 IP 位址範圍。 中樞是虛擬 WAN 建立和使用的虛擬網路。 您為中樞區域指定的位址範圍不能與任何連線的現有虛擬網路重疊。 也不能與連線至內部部署的位址範圍重疊。 如果您不熟悉位於內部部署網路設定的 IP 位址範圍，請與能夠提供那些詳細資料的人員協調。

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-wan"></a><a name="wan"></a>建立虛擬 WAN

透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。

1. 瀏覽至 [虛擬 WAN] 頁面。 在入口網站中，按一下 [+建立資源]  。 在搜尋方塊中鍵入**虛擬 WAN** 並選取 [輸入]。
2. 從結果中選取 [虛擬 WAN]  。 在 [虛擬 WAN] 頁面中，按一下 [建立]  ，以開啟 [建立 WAN] 頁面。
3. 在 [建立 WAN]  頁面的 [基本]  索引標籤中，填寫下列欄位：

   ![虛擬 WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **訂用帳戶** - 選取您要使用的訂用帳戶。
   * **資源群組** - 建立新的或使用現有的資源群組。
   * **資源群組位置** - 從下拉式清單中選擇資源位置。 WAN 是全域資源，並不會在特定區域存留。 不過，您必須選取一個區域以方便管理，以及放置所建立的 WAN 資源。
   * **名稱** - 鍵入要用來稱呼 WAN 的名稱。
   * **類型：** 標準。 如果您建立基本 WAN，則只能建立基本中樞。 基本中樞只能進行 VPN 站對站連線。
4. 填寫完欄位之後，選取 [檢閱 + 建立]  。
5. 驗證通過後，選取 [建立]  ，以建立虛擬 WAN。

## <a name="create-an-empty-virtual-hub"></a><a name="hub"></a>建立空的虛擬中樞

1. 在您的虛擬 WAN 底下選取 [中樞]，然後按一下 [+新增中樞] 

   ![新網站](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. 在 [建立虛擬中樞] 頁面上，填入下列欄位。

   **區域** - 選取您要在其中部署虛擬中樞的區域。

   **名稱** - 輸入要用來稱呼虛擬中樞的名稱。

   **中樞私人位址空間** - 採用 CIDR 標記法的中樞位址範圍。

   ![新網站](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. 按一下 [檢閱 + 建立] 
4. 在 [驗證成功]  頁面上，按一下 [建立] 

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>建立 P2S 設定

P2S 設定會定義用於連線遠端用戶端的參數。

1. 瀏覽至 [所有資源]  。
2. 按一下您建立的虛擬 WAN。
3. 按一下頁面頂端的 [+建立使用者 VPN 設定]  ，以開啟 [建立新的使用者 VPN 設定]  頁面。

   ![新網站](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. 在 [建立新的使用者 VPN 設定]  頁面上，填入下列欄位：

   **設定名稱** - 這是您要用來指稱設定的名稱。

   **通道類型** - 用於通道的通訊協定。

   **根憑證名稱** - 憑證的描述性名稱。

   **公用憑證資料** - Base-64 編碼的 X.509 憑證資料。
  
5. 按一下 [建立]  來建立設定。

## <a name="edit-hub-assignment"></a><a name="edit"></a>編輯中樞指派

1. 瀏覽至虛擬 WAN 底下的 [中樞]  刀鋒視窗
2. 選取要與 VPN 伺服器設定產生關聯的中樞，然後按一下 [...]  。

   ![新網站](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. 按一下 [編輯虛擬中樞]  。
4. 勾選 [包含點對站閘道]  核取方塊，然後挑選您想要的 [閘道縮放單位]  。

   ![新網站](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. 輸入要從中將 IP 位址指派給 VPN 用戶端的 [位址集區]  。
6. 按一下 [確認] 
7. 此作業最多可能需要 30 分鐘的時間才能完成。

## <a name="download-vpn-profile"></a><a name="download"></a>下載 VPN 設定檔

使用 VPN 設定檔來設定用戶端。

1. 在虛擬 WAN 的頁面上，按一下 [使用者 VPN 設定]  。
2. 在頁面頂端，按一下 [下載使用者 VPN 設定]  。
3. 檔案建立完成之後，您可以按一下連結來下載。
4. 使用設定檔檔案來設定 VPN 用戶端。

### <a name="configure-user-vpn-clients"></a>設定使用者 VPN 用戶端
使用下載的設定檔來設定遠端存取用戶端。 每個作業系統的程序不同，請遵循下面的正確指示：

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. 從官方網站下載並安裝 OpenVPN 用戶端。
2. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [使用者 VPN 設定] 索引標籤，或是 PowerShell 中的 New-AzureRmVpnClientConfiguration 來完成。
3. 將設定檔解壓縮。 在記事本中開啟 OpenVPN 資料夾中的 vpnconfig.ovpn 設定檔。
4. 以 Base64 的 P2S 用戶端憑證公開金鑰填入 P2S 用戶端憑證區段。 在 PEM 格式的憑證中，您只需開啟 .cer 檔案並在憑證標題之間複製 Base64 金鑰。 如需相關步驟，請參閱[如何匯出憑證來取得編碼的公開金鑰](certificates-point-to-site.md)。
5. 以 Base64 的 P2S 用戶端憑證私密金鑰填入私密金鑰區段。 如需相關步驟，請參閱[如何擷取私密金鑰](howto-openvpn-clients.md#windows)。
6. 請勿變更任何其他欄位。 使用用戶端輸入中填入的設定來連線至 VPN。
7. 將 vpnconfig.ovpn 檔案複製到 C:\Program Files\OpenVPN\config 資料夾。
8. 以滑鼠右鍵按一下系統匣中的 OpenVPN 圖示，然後按一下 [連線]。

##### <a name="ikev2"></a>IKEv2

1. 選取 Windows 電腦架構所對應的 VPN 用戶端組態檔。 若是 64 位元的處理器架構，請選擇 'VpnClientSetupAmd64' 安裝程式套件。 若是 32 位元的處理器架構，請選擇 'VpnClientSetupX86' 安裝程式套件。
2. 對套件按兩下來加以安裝。 如果您看到 SmartScreen 快顯視窗，請按一下 [更多資訊]，然後按一下 [仍要執行]。
3. 在用戶端電腦上，瀏覽至 [網路設定]，然後按一下 [VPN]。 VPN 連線會顯示其連線的虛擬網路名稱。
4. 嘗試連線之前，請確認您已在用戶端電腦上安裝用戶端憑證。 使用原生 Azure 憑證驗證類型時，必須提供用戶端憑證才能通過驗證。 如需有關產生憑證的詳細資訊，請參閱[產生憑證](certificates-point-to-site.md)。 如需有關如何安裝用戶端憑證的資訊，請參閱[安裝用戶端憑證](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)。

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>檢視您的虛擬 WAN

1. 瀏覽至虛擬 WAN。
2. 在 [概觀] 頁面中，地圖上的每個點都代表著中樞。
3. 在 [中樞與連線] 區段中，您可以檢視中樞狀態、網站、區域、VPN 連線狀態和輸入與輸出位元組。


## <a name="clean-up-resources"></a><a name="cleanup"></a>清除資源

您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源。 將 "myResourceGroup" 取代為您的資源群組名稱，然後執行下列 PowerShell 命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
